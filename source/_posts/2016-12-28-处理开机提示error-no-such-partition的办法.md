---
title: '处理开机提示error:no such partition的办法'
tags:
  - windows
categories:
  - Operating system
keywords:
  - windows
  - no such partition
description:
  - windows error:no such partition
toc: true
date: 2016-12-28 15:10:23
thumbnail:
banner:
---


#### 问题原因
出现这种提示是由于找不到系统引导引起的。

一般我们在安装windows和linux双系统时，都是先安装windows然后再安装linux，这样我们就导致了我们是从linux引导windows的，这时系统的引导是放在linux系统里的，而当我们从windows系统中删除linux系统后，电脑在开机时就找不到系统引导，从而导致无法启动

#### 解决办法
1. 插入启动盘（U盘或者时光盘），开机选中从U盘启动（如果是光盘就从光盘启动）
2. 选择左下角的“修复计算机”
3. 选择命令提示符
4. 输入命令：
```
bootsect/nt60 C：
```
5. 接着再输入:
```
bootrec/fixmbr
```
6. 重新启动就好了