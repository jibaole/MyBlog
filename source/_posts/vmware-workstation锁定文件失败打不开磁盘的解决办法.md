---
title: vmware workstation锁定文件失败打不开磁盘的解决办法
tags:
  - vmware
categories:
  - Virtualization technology
toc: true
date: 2013-11-09 17:17:50
description: 参考：http://www.2cto.com/os/201411/355373.html
feature:
---

#### 报错原因

因为虚拟机在运行的时候，会锁定文件，防止被修改，而如果突然系统崩溃了，虚拟机就来不急把已经锁定的文件解锁，所以你在启动的时候，就会提示无法锁定文件。

#### 解决方法

在你的虚拟机的安装目录下，搜索 .lck文件删除这三个搜索到的文件即可.

