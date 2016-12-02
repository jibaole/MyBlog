---
title: tcpdump和snoop抓包指令详解
tags:
  - shell
categories:
  - Scripting language
toc: true
date: 2013-06-14 17:10:52
description: 抓包命令需要在root用户下执行
feature:
---

#### tcpdump(适用linux、hp-unix(需要自己安装)、aix)
```
tcpdump -s 0 -i eth0 udp port 8018 and host 192.168.27.208 -w trap.dat
tcpdump -s 0 -i lan1 udp port 514
```
>参数解释:
-s:抓取数据包时默认抓取长度为68字节。加上-S 0 后可以抓到完整的数据包
-i:只抓取经过网卡eth0的包
udp:用来过滤数据报的类型,可以为tcp、udp、icmp等.
port 8018:指定本段端口
host 192.168.27.208:指定源端地址
-w:保存成dat文件，方便使用wireshark等专业抓包分析软件查看.
<!-- more -->
#### snoop(适用solaris)
```
snoop -o syslog.dat -d ce0 udp port 514 
```
>参数解释:
-o:保存成dat文件，方便使用wireshark等专业抓包分析软件查看.
-d:只抓取经过网卡ce0的包
port:指定本段端口