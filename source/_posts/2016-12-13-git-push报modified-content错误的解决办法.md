---
title: git push报modified content错误的解决办法
tags:
  - git
categories:
  - Version control
keywords:
  - git
  - push
  - modified content
description:
  - git
  - push
  - modified content
toc: true
date: 2016-12-13 17:24:02
thumbnail:
banner:
---

#### 报错信息
```
modified: xxx(modified content, untracked content)
```

#### 报错原因
xxx目录报错访问github项目找到指定的目录会发现该目录下是空的，也就是说这个目录下面的内容没有被追踪.检查发现，在该目录下存在一个.git目录。

#### 解决办法
``` bash
cd xxx
rm -rf .git
```