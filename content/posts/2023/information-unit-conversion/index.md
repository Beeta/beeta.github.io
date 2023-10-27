---
title: "信息量转换" #标题
date: 2023-10-27T14:57:10+08:00 #创建时间
lastmod: 2023-10-27T14:57:10+08:00 #更新时间
author: ["Beeta"] #作者
categories: 
- 计算机知识
tags: 
- 杂谈
description: "" #描述
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

## 一个例子

<img src="./assets/imageDownloadAddress-20231027150324304.jpeg" alt="img" style="zoom:50%;" />

Mac中的一个文件，显示大小为：1884463字节，显示MB大小位1.9。

但是，1884463/1024/1024 = 1.797…，这是什么原因呢？

**我们再使用**`du`**来查看这个文件**：

<img src="./assets/imageDownloadAddress.png" alt="img" style="zoom:67%;" />

又不一样了，这是为什么呢？使用 man du 可以看到如下说明

<img src="./assets/imageDownloadAddress.jpeg" alt="img" style="zoom:67%;" />

## 两个标准：IEC和SI

IEC：国际电工委员会。1999年1月，[国际电工委员会](https://zh.wikipedia.org/wiki/国际电工委员会)（IEC）引入了“kibi-”、“mebi-”、“gibi-”等词头以及缩写符号“Ki”、“Mi”、“Gi”等来明确说明二进制乘数计数。

SI：International System of Units，国际单位制，也就是：

<img src="./assets/imageDownloadAddress-20231027150324674.jpeg" alt="img" style="zoom:50%;" />

也就是说：

```plain
1GB = 10^3 MB = 10^6 KB = 10^9 B   # 优盘，磁盘厂家常用
1GiB = 2^10 MiB = 2^20 KiB = 2^30 B
```

而我们平常中的  GB/MB/KB等的1024转换实际上是以讹传讹就这么用下来了。

## 两个领域：计算机和通信

上面说的GB/MB实际都属于`计算机领域`，

而我们常用的**比特率bps**则属于`通信领域`，在通信领域，一般遵守SI规范，即换算为1000，如：

```plain
1Gbps = 10^3 Mbps = 10^6 kbps = 10^9 bps
```

> 请再次注意上面的大小写。这也是SI 的标准

<img src="./assets/imageDownloadAddress-20231027150327413.jpeg" alt="img" style="zoom:50%;" />

## 总结一下

1. 计算机领域一般以2^10为进制，通信领域一般以10^3为进制，通信领域一般遵守SI标准
2. 1GB=1024MB其实是错的，准确的是 1Gib=1024MiB
3. 在电信领域，没有`g，m，K`，只有`G，M，k` (学术上)
4. 1Mbps = 1000kbps

## 参考

https://zh.wikipedia.org/wiki/国际单位制词头
