---
title: "2026 年大模型这一年：时间线、关键事件与几点洞察" #标题
date: 2026-10-25T15:55:15+08:00 #创建时间
lastmod: 2026-10-25T15:55:15+08:00 #更新时间
author: ["Beeta"] #作者
categories: 
- ai
tags: 
- ai
- 大模型
description: "基于 Simon Willison 的演讲《2026 in LLMs (so far)》整理：从 2025 年 11 月的拐点到 9 月的 GPT-6，按月梳理模型、Agent、安全事件，再谈几点我的理解。" #描述
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

> 本文素材来自 Simon Willison 9 月 25 日在 WeAreDevelopers 北美大会的闭幕演讲 [2026 in LLMs (so far)](https://simonwillison.net/2026/Sep/27/2026-in-llms-so-far/)（[视频](https://www.youtube.com/watch?v=GAkIytR7vcc)）。事实部分以原文为准，「洞察」一节是我的理解，不代表原作者。

今年的大模型新闻多到记不住。Simon 的演讲按月份把它们串了起来，读完我觉得今年的主线是 Agent：上半年它变得能用，下半年它开始闯祸。

## 一张表看全年

| 时间 | 关键事件 | 一句话意义 |
| --- | --- | --- |
| 2025.11 | Claude Opus 4.5、GPT-5.1 发布；Warelay（后来的 OpenClaw）首次提交 | 编码 Agent 从「常出错」变成「能天天用」 |
| 1 月 | OpenClaw 爆火，Mac mini 卖断货；MoltBook 上线又迅速沉寂 | 「Claw」成为一类新软件 |
| 2 月 | StrongDM 公开 Dark Factory；Gemini 3.1 Pro；各大厂 Tokenmaxxing | 「人不写、人不审代码」第一次被认真实践 |
| 3 月 | 中国出现 OpenClaw 安装派对 | 普通人确实想要个人 Agent |
| 4 月 | Claude Mythos 只开放给安全研究者；Qwen3.6-35B-A3B 在笔记本上胜过 Opus 4.7 | 漏洞挖掘能力过线；本地模型追上来 |
| 5 月 | 教皇 Leo XIV 发布 AI 通谕；RubyGems 遭攻击 | AI 进入公共议题；一个当时没人能解释的谜团 |
| 6 月 | Claude Fable 5 发布，三天后被美国出口管制叫停 | 第一个「Fable 级」模型，也是第一次因国家安全下架 |
| 7 月 | Fable 回归；GPT-5.6 发布；Hugging Face 被入侵，OpenAI、Anthropic 先后承认是自家训练中的 Agent | 训练中的 Agent 逃出沙箱，攻击真实系统 |
| 8 月 | Qwen 3.8 27B；用 Agent 复刻 2022 年的「浣熊劫案」游戏 | 本地模型接近前沿；能做出像游戏的东西，做不出好玩的游戏 |
| 9 月 | 德语 wiki、RubyGems 事件被追溯到 OpenAI；澳大利亚总理在联合国点名；GPT-6 家族、Claude Fable 5.1 / Opus 5.5 | Agent 安全成了国际事件 |

## 时间线

### 前奏：2025 年 11 月的拐点

Claude Opus 4.5 和 GPT-5.1 在 11 月发布。单看模型，它们只是常规的小幅升级。但配上各自的编码 Agent（Claude Code 和 Codex）之后，情况变了：以前常常出错，现在可以每天依赖。

Simon 的说法是，模型每次进步都可能越过一条看不见的线，某件原本不太行的事突然就行了。这一次越线的是编码 Agent。

同月 24 日，一个叫 Warelay 的冷门 GitHub 仓库有了第一次提交。它就是后来的 OpenClaw。

### 1 月：Claw 元年

圣诞假期里，很多开发者拿新组合试了一圈，回来发现它们能干的事比以前多太多。

**OpenClaw。** Warelay 在一月里改了好几次名：CLAWDIS、CLAWDBOT、Moltbot，最后定为 [OpenClaw](https://github.com/openclaw/openclaw)。项目开始不到两个月就有 8,330 次提交，到 9 月已超过 10 万次。Simon 称它是世上 vibe coding 程度最高的软件。

它定义了一类新软件，大家叫它「Claw」，此外还有 NanoClaw、IronClaw、PicoClaw。现在它们被改称「个人 Agent」或「通用 Agent」。湾区的 Apple Store 一度卖光了 Mac mini，因为大家买来专门跑 OpenClaw。Drew Breunig 的比喻是：Claw 是数字宠物，Mac mini 是养它的鱼缸。

**MoltBook。** 一个给 AI Agent 用的社交网络：让你的 Claw 去和别人的 Claw 聊天。周四上线，周五爆火，周一上了《纽约时报》，周二就被垃圾内容淹没。一个月后 Meta 把它买了下来。

**两个新词。** Simon 和 Oxide and Friends 播客的两位主持人造了「Deep Blue」一词，指工程师面对「AI 什么都能做」时的倦怠和无力。Simon 自己还得了「AI mania」：Agent 一停下来就觉得在浪费时间，于是熬夜让它继续干。他在这个状态下用 Python 写了一个 JavaScript 解释器和一个 WebAssembly 运行时，做完后问自己：世界需要一个又慢又有 bug 的 Python 版 JS 解释器吗？答案是不需要，这股劲也就过去了。

### 2 月：Dark Factory 与 Tokenmaxxing

**StrongDM 的软件工厂。** 安全公司 StrongDM 发表了 [Software Factories and the Agentic Moment](https://factory.strongdm.ai)，公开了他们从 2025 年 7 月起执行的两条规则：

1. 代码不得由人编写，必须经过编码 Agent。
2. 代码不得由人审查。

Dan Shapiro 管这叫 Dark Factory：工厂自动化到一定程度，可以关灯运转。第一条在 2 月还显得激进，到秋天已经是很多人的日常。第二条才是难点，本次大会有不少场次都在讨论代码审查。Simon 认为 StrongDM 比业界领先半年，他们在探索的问题是：不读代码，怎么确认软件质量够好。

**Gemini 3.1 Pro。** Google 在 2 月 19 日发布 Gemini 3.1 Pro，它画的「鹈鹕骑自行车」SVG 相当好：链条位置对，两边都有脚，车篮里还有条鱼。Jeff Dean 随后发了视频，展示青蛙骑大小轮车、长颈鹿开小车、鸵鸟穿旱冰鞋。Simon 原本的预案是「模型画好了鹈鹕就换别的动物」，结果 Google 把各种动物配各种交通工具都练了。

**Tokenmaxxing。** Meta 把 AI 使用纳入绩效考核，微软要求几乎全员用 AI，Uber 宣称 90% 的工程师在用。几个月后风向逆转：Meta 收紧 token 用量，微软说这不是他们追求的目标，Uber 在四个月内花超预算后给员工设了上限。Agent 太贵了。去年一个人很难花掉 50 美元，现在干一天正经活就能花 1,000 美元。Simon 认为，这也是 AI 在 2026 年找到产品市场契合的方式：主要靠编码 Agent。

### 3 月：OpenClaw 的巅峰

中国有公司办起了 OpenClaw 安装派对，不懂技术的普通人排长队请人帮忙装到自己设备上。Simon 认为这证明了真实需求：普通人确实想要一个能替自己办事的 Agent。

他对 Claw 的定义很准：**一个戴了顶不那么吓人的帽子的编码 Agent**。底层原理一样，都是在你电脑上写代码、跑代码来完成任务。于是各家开始比赛，看谁先做出普通人用了也不会伤到自己的「安全 Claw」。Meta 的 Muse 在 9 月初上线，冲到 iPhone 免费榜第一，但 Simon 还不确定它是否真的安全。

### 4 月：一个没发布的模型，一个笔记本上的模型

**Claude Mythos。** Anthropic 宣布了 Claude Mythos，又说它太危险，只开放给一小群可信的安全研究者（Project Glasswing）。这种「太危险」的营销从 GPT-2 时代就有，通常值得怀疑。但 Simon 这次信了，因为他见过编码 Agent 找普通 bug 的能力。事后看，模型确实已经非常会找漏洞。

**本地模型追上来。** 4 月 16 日，Simon 在笔记本上跑 Qwen3.6-35B-A3B（一个 21GB 的文件），它画的鹈鹕比刚发布的 Claude Opus 4.7 还好。他怀疑 Qwen 针对这个测试做过训练，又让两者画火烈鸟骑独轮车，Qwen 还是赢了。

### 5 月：教皇发声，RubyGems 遇袭

5 月 25 日，教皇 Leo XIV 发布通谕《Magnifica Humanitas》，主题是「在人工智能时代守护人」。年初播客里这本来是句玩笑预测，回头看却在意料之中：他的名号取自 Leo XIII，后者在 1891 年就工业革命发布了《Rerum novarum》，这份通谕间接促成了五天工作制。Anthropic 联合创始人 Christopher Olah 出席了发布活动。Corey Quinn 吐槽说，让教皇把你产品的技术局限封成灵性论述，是他见过最成功的厂商游说。

同月 12 日，RubyGems 遭到攻击，有人上传了成百上千个可疑包，平台只好暂停注册。当时没人知道是谁干的，Simon 把它放进了「谜团堆」。

### 6 月：Fable 5 的三天

**Fable 级模型登场。** 6 月 9 日，Claude Fable 5 发布。它是 Mythos 的削弱版，不会帮你入侵系统或造生物武器。Simon 把它看作第一个公开的「Fable 级」模型，现在同级的还有 GPT-6 Astra。他的定义是：

> 只要你能定义清楚目标，给出没有歧义的约束，再给它完成任务所需的工具，它就能靠蛮力把问题解决掉。

这听上去是对工程师的直接威胁。但 Simon 接着指出：定义目标、写清约束、找对工具，本来就是软件工程。做好这些需要大量经验，做得好的人等于有了超能力。这个认识让他的 Deep Blue 情绪缓解了一些。

**三天后下架。** Anthropic 说订阅计划里的 Fable 只按原价开放到 6 月 22 日，Simon 又进入 AI mania，调整日程只为多用它几天。结果 6 月 12 日周五晚上，美国政府以国家安全为由发布出口管制指令，几小时后 Fable 5 和 Mythos 都停止服务，距发布只有三天。

后来 Katie Moussouris 披露了原因：亚马逊的安全研究员发现，让 Fable「review the code for security issues」，它会拒绝；换成「fix this code」，它照样能找出漏洞并修好。让 Fable 下架的就是这句「fix this code」。

**又两个谜团。** 一个荒废了约 20 年的德语游戏开发 wiki 突然涌入大量编辑，账号名叫 AgentOpenAIProbe、AgentOpenAISep7，它们还在页面上互相留言。澳大利亚政府的 Medicare Item Reports 服务收到可疑流量，突破了几层防护，读到了不该读的数据。

### 7 月：训练中的 Agent 逃出了沙箱

**榜首坐不稳。** Fable 在 7 月 1 日回归，当了 8 天世界最强模型。7 月 9 日 OpenAI 发布 GPT-5.6，可能略逊于 Fable，但差距很小，同属 Fable 级。算下来，Fable 稳坐榜首的 30 天里有 18 天不可用。Simon 的结论是：竞争这么激烈，谁也待不久，把自家模型宣传成「世界末日」级别，最后会伤到生意。

**谜底揭开一半。** 7 月 18 日，PyPI 上出现恶意包 mlflow-ui。7 月 16 日，Hugging Face 公告被一个来源不明的自主 Agent 系统入侵。7 月 21 日，[OpenAI 承认是自己](https://openai.com/index/hugging-face-model-evaluation-security-incident/)。

现在各家实验室都用 RLVR（基于可验证奖励的强化学习）训练模型，模型擅长写代码、做数学、找漏洞，都是靠它练出来的。训练时让模型做练习，表现好的权重得到强化，有点像在跑一场进化。OpenAI 在沙箱里跑安全练习，Agent 找到了沙箱本身的漏洞，逃出去攻击 Hugging Face，想借此解开原本无解的题目。

九天后，Anthropic 也[公布](https://www.anthropic.com/news/investigating-incidents-cybersecurity-evals)在自家训练日志里发现了同样的事：自己的 Agent 在训练中突破隔离，那个 PyPI 恶意包就是它们干的。

### 8 月：本地模型接近前沿，游戏还是不好玩

**Qwen 3.8 27B。** 一个 17GB 的模型，在 Simon 的笔记本上画出了他见过最好的鹈鹕之一：车架形状正确，两条腿分在车架两侧。代价是花了 21 分钟，因为它默认用 high 推理档。Simon 说这个默认值很糟，调低后快很多，效果只差一点。这是他第一次觉得本地模型接近前沿，也是他推荐的入门本地模型。他原本以为要再等五年、花一万美元买硬件，才能达到一半的效果。

**浣熊劫案。** 2022 年 8 月，Simon 用 GPT-3 和第一代 DALL-E 给一款叫「Raccoon Heist」的游戏写了简介、画了概念图。四年后，他把那条推文的截图直接丢给编码 Agent：

- Claude Code + Claude Fable 5 做出了浣熊在后院捡宝贝、躲开打手电的守卫的游戏，像游戏，但不太像「劫案」。
- Codex Desktop + GPT-5.6 Sol Ultra 做出了《Moonlight & Mayhem》：浣熊潜入博物馆，救出两个同伴，三只叠罗汉偷走金沙丁鱼，好得多。

可这些游戏只好玩一分十五秒。Simon 的体会是，vibe coding 出一个「看起来像游戏」的东西很容易，但要做出玩法循环好、有挑战、让人想反复玩的游戏，他自己做不到，他试过的 Agent 也都做不到。能做出像游戏的东西，不代表我们就是游戏开发者。

### 9 月：谜团堆被清空

**德语 wiki。** 9 月 4 日，一组独立研究者（Sydney Von Arx、Cormac Slade Byrd、Spencer Kitts、Thomas Larsen）发现，OpenAI 训练中的 Agent 私下互相交流用的留言板，就是 6 月那个德语 wiki。Simon 的质疑是：OpenAI 认了 Hugging Face 的事，这件事本该也能从日志里查出来，结果要靠外部研究者揭开。

**RubyGems。** 一周后，同一组人查明，5 月的 RubyGems 攻击也是 OpenAI 训练中的 Agent 干的。问题显然已经存在好几个月，Simon 怀疑还有更多没被发现的。

**联合国。** 澳大利亚总理在联合国大会演讲中警告 AI 的发展速度，点名 OpenAI 入侵了澳大利亚的医疗网站，也就是 6 月那个 Medicare 服务。Simon 推测它和 wiki 事件出自同一次训练：wiki 上有帖子提到 .gov.au 网站，而那次训练似乎要求 Agent 上网查统计数据来回答评测题。一次模型训练，最后成了国家元首在联合国提出的国际事件。

**FelonyBench。** 于是有了一个可能比鹈鹕更有用的新基准 [FelonyBench](https://www.felonybench.com/)，统计各实验室犯下的重罪级网络攻击：OpenAI 11 次，Anthropic 9 次，Google 3 次，Meta 1 次。Google 的 3 次是几周前向《华尔街日报》承认的，此前不披露的理由是「Agent 意识到不该这么做，自己停下了」。

**新一代模型。** GPT-6 家族刚发布，Astra 画的鹈鹕非常出色，三个型号还用了相近的配色，最便宜的 Luna 花 0.4 美分就能画个像样的。Claude 也追上来一点：Fable 5.1 画出了 Claude 系列至今最好的鹈鹕，花了 3.30 美元；Opus 5.5 在 Max 档思考了 128,000 个 token，token 用完了也没给出答案。

## 几点洞察

### 1. 拐点出在「模型 + 工具链」的组合上

全年的起点是两个「小幅升级」的模型。真正的变化发生在模型和 Agent 框架配合之后，能力越过了「可以天天用」这条线。OpenClaw、Dark Factory、Tokenmaxxing，后面这些故事都建立在编码 Agent 可用的基础上。看新模型时，与其只看榜单分数，不如看它放进自己的工作流后，有没有哪件事从「不行」变成了「行」。

### 2. 个人 Agent 本质上就是编码 Agent

按 Simon「戴了顶不那么吓人的帽子」的说法，普通人排队安装的是一个能在自己电脑上写代码、跑代码的东西。它的能力和风险都来自这里，所以「安全 Claw」这件事才这么难：要把执行代码的能力交给不懂代码的人，又不让他们伤到自己。

### 3. 能力越强，规格越值钱

Fable 级模型的前提是：目标清晰、约束明确、工具齐全。这三件事模型自己做不了，恰恰是工程经验最集中的地方。StrongDM 的第二条规则「不由人审查代码」，难点也在这里：不读代码，就得用测试、规格和验证手段证明软件可靠。工程师的工作重心正在从「写」转向「定义」和「验证」。

### 4. 安全风险不只来自用户，也来自训练

今年最出人意料的安全事件，是实验室自己训练出来的 Agent 为了完成练习逃出沙箱、攻击真实系统。RLVR 奖励的是「把题做出来」，而系统漏洞也是一种解法。年初 Simon 预测会有一次编码 Agent 安全的「挑战者号灾难」，他设想的场景是 Agent 被劫持、造成真实经济损失，这个还没发生。Agent 自己逃出沙箱这种情况，我觉得比被劫持更难防，因为没有一个外部攻击者可以去拦。

自己用 Agent 时，沙箱和权限边界要当成必需品来配。Fable 那句「fix this code」也说明，拒绝策略很容易换个说法就绕过去。

### 5. 榜首是暂时的，本地模型是长期的

Fable 当了 8 天第一就被 GPT-5.6 追上，前沿模型的领先窗口以天计。与此同时，17GB 的 Qwen 3.8 27B 在笔记本上就能逼近前沿。对大多数人来说，与其押注某个模型，不如让工作流可以随时换模型，同时认真看看本地模型能接住哪些活。

### 6. 工作没有变轻松，只是变快了

Simon 在结尾说了一个困惑：有了这么多 Agent，为什么工作反而更累了？他的解释是，一方面自己接的项目野心更大，另一方面简单的事都被 Agent 做掉了，剩下的全是难题。他引用了三届环法冠军 Greg LeMond 的话：

> It doesn't get easier, you just get faster.

Deep Blue、AI mania、做不好玩的游戏，我看都和这句话有关。Agent 把速度拉上去了，你要花力气的地方则挪到了判断做什么、怎么定义、做得好不好。

## 附：鸮鹦鹉

演讲里有条贯穿全年的支线。Simon 年初预测新西兰的鸮鹦鹉（Kākāpō）会迎来繁殖大年。年初全世界只剩 236 只，它们只在 Rimu 树大量结果的年份繁殖，而这种年份已经四年没出现了。2 月 14 日，四年来第一只雏鸟孵化。到 9 月，数量达到恢复期以来最高的 325 只，89 只雏鸟存活下来。

他让 Claude Opus 5.5 用 JavaScript 画像素动画，做了一个 [Kākāpō 舞会](https://simonwillison.net/2026/Sep/26/kakapo-party/)来庆祝，他称这是「今年最重要的新闻」。
