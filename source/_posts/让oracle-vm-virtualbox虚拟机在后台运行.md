---
title: 让oracle vm virtualbox虚拟机在后台运行
tags:
  - virtualbox
categories:
  - Virtualization technology
toc: false
date: 2015-10-16 16:30:10
description:
feature:
---

1. 运行Oracle VM VirtualBox虚拟机，记下你要在后台运行的是哪一个虚拟机，比如我这里运行的是 “2003“ 这一个

2. 记录虚拟机的安装路径，当然最简单的办法就是在快捷方式上面点右键，查看属性了
<!-- more -->
3. 新建一个文本文件，输入以下内容，并另存为 bat文件,当然中间的那个2003要改为第一步记下的那个name
```
@echo off
cd C:\Program Files\Oracle\VirtualBox 
vboxmanage startvm 2003 -type headless
pause
```
4. 运行这个批处理就OK啦。另外附上关机的批处理
```
@echo off
cd C:\Program Files\Oracle\VirtualBox 
vboxmanage controlvm 2003 poweroff
pause
```