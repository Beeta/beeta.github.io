---
title: "OSC 7501：让程序自己告诉终端它在干什么" #标题
date: 2026-10-24T00:12:01+08:00 #创建时间
lastmod: 2026-10-24T00:12:01+08:00 #更新时间
author: ["Beeta"] #作者
categories: 
- 技术
- 工具
tags: 
- ai
- 效率
description: "Ghostty 作者 Mitchell Hashimoto 提出的终端转义序列 OSC 7501（Program Status Protocol）：程序通过 pty 直接上报空闲、运行、等待、完成、失败等状态，不用再靠解析屏幕去猜。文末补充 Kero 终端的支持情况。" #描述
weight: # 输入1可以顶置文章，用来给文章展示排序，不填就默认按时间排序
slug: ""
draft: false # 是否为草稿
comments: true #是否展示评论
showToc: true # 显示目录
TocOpen: true # 自动展开目录
hidemeta: false # 是否隐藏文章的元信息，如发布日期、作者等
disableShare: true # 底部不显示分享栏
showbreadcrumbs: true #顶部显示当前路径
cover:
    image: "" #图片路径：posts/tech/文章1/picture.png
    caption: "" #图片底部描述
    alt: ""
    relative: false
---

Mitchell Hashimoto（Ghostty 作者）前几天发了篇文章 [A Terminal Protocol for Program Status: OSC 7501](https://mitchellh.com/writing/program-status-osc7501)，提出一个新的终端转义序列：OSC 7501，名字叫 Program Status Protocol，完整规范见 [这里](https://superlogical.com/rex/docs/build/program-status)（目前是 0.4 草案）。程序用它告诉终端自己现在处于什么状态：空闲、运行中、在等用户、已完成、已失败，还可以附上原因。规范是通用的，不绑定任何产品。

## 为什么需要它

在终端里跑长任务太常见了：构建、部署、升级依赖、处理数据，现在又多了编码 Agent。任务一跑起来，人就切走去干别的，然后希望它跑完或者需要我拍板的时候能叫我一声。

现在的做法基本靠猜：看前台进程有没有变，等输出安静一段时间。这些办法都不通用，也没法同时表达进度、阻塞、完成和任务树。把现有的几个转义序列拼起来用，同样靠不住。

### 以 Agent 收件箱为例

最近冒出来一类工具，作者叫它 agentic inbox：把所有 Agent 的状态汇总到一个界面里，比如 Herdr、cmux、Agent Deck。它们拿状态的办法只有两种。

**启发式。** 解析屏幕内容或窗口标题。Herdr 用一份 TOML 规则判断 Claude Code 的状态，其中一条是：标题以 Braille 或半圆 spinner 字符开头，就算 working。这份规则三个月里光为 Claude Code 就改了十次。作者特意说明，这不是在批评 Herdr。

**带外 API。** 比如 Herdr 的 socket API、`cmux notify`。程序自己上报，结果准确，代价是每个程序都得分别对接每个收件箱；本地 socket 到了 SSH 或容器里还要额外桥接。而 pty 本来就能穿过这些环境。

## 协议长什么样

```
ESC ] 7501 ; <key=value 列表> ESC \
```

键值对之间用 `:` 分隔。下面是 Terraform 在等用户确认 apply，`msg` 是 base64 编码的提示文字：

```
ESC ] 7501 ; state=blocked:kind=permission:app=terraform:msg=QXBwbHkgMyB0byBhZGQsIDEgdG8gY2hhbmdlLCAwIHRvIGRlc3Ryb3k/ ESC \
```

必填的只有 `state`：

| state | 含义 |
| --- | --- |
| `idle` | 空闲，等用户下一条指令 |
| `working` | 运行中，可带 `progress` 百分比 |
| `done` | 完成了，用户还没看结果 |
| `blocked` | 要用户操作才能继续，用 `kind`（`permission` / `question` / `auth`）说明要什么，`msg` 说明原因 |
| `error` | 失败并停止 |
| `clear` | 删掉记录 |

可选键有 `app`（机器可读的程序名，如 `cargo`、`claude-code`）、`title`、`msg`，后两个用 base64 编码。

### 记录和任务树

终端为每个窗口、标签页或分屏维护一组记录。不带 `id` 的报告写进根记录；带 `id` 的写进对应子记录，`id` 用 `/` 分层，`build/test` 的父级是 `build`。一个部署工具可以这样报：根记录 working，`us-east` 在推镜像、进度 40%，`eu-west` blocked、等生产审批。具体怎么展示，终端自己决定。

几条规则：

- 每次报告整条替换对应记录，没写的键就没了，所以 `app`、`title` 每次都要重发。
- `working` / `blocked` 在进程退出或出现新的 shell 提示符时作废；`done` / `error` 会留下来，直到用户回来看过。
- 没有心跳。用户中断时程序报 `idle`，退出前报 `done` 或 `error`。
- `clear` 会连子记录一起删；不带 `id` 就清空全部。
- `state` 缺失或不认识，整条报告丢弃，这样以后新增状态也不会被老终端误当成 idle。

### 特性检测

程序发 `OSC 7501 ; ? ST`，支持的终端回一个 `?`。检测可以不做，因为终端本来就要忽略不认识的 OSC，直接发就行。想检测又不想傻等超时，就在查询后面紧跟一个 `CSI c`：所有终端都会回它，如果它的回复先到，说明终端不支持 7501。

### 限制和安全

整个序列不超过 4096 字节，`msg` 解码后不超过 2048 字节，一个终端最多 256 条记录，超出任何一项整条丢弃。报告内容一律当作不可信输入：解码后含控制字符就拒绝，不当标记语言解析；终端唯一会回写的只有特性检测的应答，程序读不到别人的记录；由状态触发的桌面通知、声音要限速。

## 程序这边怎么接

不需要 SDK、socket、环境变量，也不需要 JSON。原文用几行 sh 包了一下 rsync：

```sh
status() {
  printf '\e]7501;state=%s:msg=%s\e\\' "$1" "$(printf '%s' "$2" | base64 | tr -d '\n')"
}
status working "Syncing photos"
rsync -a ~/Photos backup:/photos && status done "Photos synced" || status error "rsync failed"
```

终端不认识这个序列就直接忽略，所以在哪儿发都安全。

## 现有的序列为什么不够用

- **OSC 9;4（进度条）**：只能表达忙、百分比、出错，说不了「在等你」，也不带消息和程序身份。它还和 iTerm2 的通知序列共用编号 9，只认其中一个的终端会显示乱码。
- **OSC 9 / 99 / 777（桌面通知）**：一次性事件，通知弹完终端就不记得了，不知道程序还在不在等。
- **OSC 133（shell 集成）**：由 shell 发，只知道命令开始、结束和退出码，看不到程序内部在干什么。
- **OSC 0 / 2（窗口标题）**：一个无结构字符串，程序往里塞 spinner，机器没法可靠解析。

7501 的分工是：程序最清楚自己的状态，由它上报；终端只负责保存，怎么展示自己决定。

## 现状

作者已经在 libghostty 和 Rex 里实现了，还用插件或 fork 的方式给 Terraform、Claude Code、Codex、Homebrew 做了概念验证，每处改动不超过十几行。注意这些只是作者的 PoC，还不是这些项目的官方支持。规范评审有好几个终端和终端程序的维护者参与。

作者的结论是一句话：

> The program already knows. Let's give it a way to tell us!

## Kero 已经支持

终端 [Kero](https://kero.sh) 已经支持 OSC 7501。Kero 是专门给编码 Agent 用的终端，侧边栏显示每个会话处于 working、waiting、idle、done 还是 failed，`⌘J` 直接跳到等你的那个会话。

Kero 判断状态有两条路（见 [状态文档](https://kero.sh/docs/status)）：

- **被动识别**：对 Claude Code、Codex 这类编码 Agent，看屏幕上的 spinner、权限确认框、输入提示符；对其他程序，看标题里有没有 spinner。就是上文说的启发式那一套。
- **主动上报**：程序自己发 OSC 7501。

Kero 自带一个 `kero report` 命令，底层写的就是 OSC 7501 序列：

```sh
kero report working "Migrating the database" --progress 40
kero report blocked "Approve the production deploy?" --kind permission
kero report done "Deployed to staging"
```

`blocked` 会让会话变成 waiting，人不在时 `done` 和 `blocked` 会发通知。一个窗格里有多个任务时用 `--id`、`--title` 区分，窗格显示优先级最高的那条：blocked > working > error > done。

这个协议不归 Kero 所有。不用 `kero report`，直接用上面那段 `printf` 也一样，换到别的支持 7501 的终端照样能用。用 `OSC 7501 ; ?` 探测时，Kero 会原样回应。所以给自己的部署脚本加几行 `status`，在 Kero 里就能直接看到它卡在哪一步。

实际用下来，这个能力很丝滑。有状态变化，Kero 会在右上角提醒一下，不打断手上的事，用着挺舒服。体验上不太像是终端做的东西，但它确实就是终端在做的事。
