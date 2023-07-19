---
title: "关于IPython的几个冷知识" #标题
date: 2023-07-19T15:23:05+08:00 #创建时间
lastmod: 2023-07-19T15:23:05+08:00 #更新时间
author: ["Beeta"] #作者
categories: 
- 技术
tags: 
- python
- ipython
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
一直在用ipython，有几个很重要的tip是今天才知道。

## 使用python -m 打开ipython

因为ipython在安装时候已经写入到了python命令同目录下，所以平常直接`ipython`就可以进入了。但最近突发奇想，想用`python -m ipython`打开，却发现打不开

```bash
python3 -m ipython
# 报错：/xxxxxx/bin/python: No module named ipython
```

研究了好久才发现，原来需要有大小写！

```bash
python3 -m IPython
```

那新问题又来了，我怎么知道我装的模块实际名字是什么呢？不去查谁知道ipython的正式名字是IPython呢。我们可以用`pkgutil`这个内置模块进行查询

```python
import pkgutil

for module in pkgutil.iter_modules():
    if "ipython" in module.name.lower(): # 注意把模块名都小写化，同时去除这个条件也可以输出所有的模块名字
        print(module.name) 
```

## ipython -i 用法

之前没用过，在研究上面问题的时候看到了。这个参数的含义是加载一个py文件并进入交互系统，作用就是进入ipython之后，我们就可以直接调用py文件内定义的变量和函数了。

比如有个文件`script.py`

```python
# script.py
x = 10
y = 20

def add(a, b):
    return a + b

```

我们使用`python3 -m IPython -i script.py` 或者 `ipython -i script.py` 进入ipython

```text
In [1]: x
Out[1]: 10

In [2]: y
Out[2]: 20

In [3]: print(add(3,5))
8

```

## 未完待续

