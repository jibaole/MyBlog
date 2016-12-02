---
title: scp命令的使用方法
tags:
  - shell
categories:
  - Scripting language
description: 记录linux中scp命令的简单使用方法
toc: true
date: 2013-11-09 16:44:32
feature:
---

#### 将本机文件复制到远程服务器上

```
scp /home/guoqing/script.tar test@10.11.209.121:/home/a/dx/c/HostMonitor
```
/home/a/dx/c/HostMonitor    本地文件的绝对路径
script.tar    要复制到服务器上的本地文件
test    通过test用户登录到远程服务器
10.11.209.121     远程服务器的IP地址
/home/a/dx/c/HostMonitor    将本地文件复制到位于远程服务器上的路径

#### 将远程服务器上的文件复制到本机
```
scp test@10.11.209.147:/home/a/script.tar /home/a/dx/c/HostMonitor
```
test    通过test用户登录到远程服务器
10.11.209.147     远程服务器的IP地址
/home/a/script.tar    欲复制到本机的位于远程服务器上的文件
/home/a/dx/c/HostMonitor    将远程文件复制到本地的绝对路径

