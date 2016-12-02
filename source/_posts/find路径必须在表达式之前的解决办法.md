---
title: find路径必须在表达式之前的解决办法
tags:
  - shell
categories:
  - Scripting language
toc: true
date: 2015-11-21 10:10:20
description: 
feature:
---

1. 可cd到其它目录再查找(当前目录下存在要查找的目标文件时会出现此类错误，换到其它目录下再执行相同的命令即可)；

2. 如果目标文件开头用通配符'*'表示，则需转义。'*'前加'\'即可

转自：http://blog.csdn.net/u011068838/article/details/14002561
