---
title: 使用iptables实现远程连接和本地连接的端口转发
tags:
categories:
  - Network proxy
toc: true
date: 2011-08-11 11:37:56
description: 因为iptables在处理本地连接和远程连接的方法不同，所以需要分开处理
feature:
---


#### 远程连接的端口请求转发
远程连接指的是由另外一台机器连接到这台机器上。这种连接的数据包在 iptables 会首先经过 PREROUTING 链，所以只需在 PREROUTING 链中作 DNAT(Destination Network Address Translation)
```
iptables -t nat -A PREROUTING -p tcp -i eth0 -d 192.168.4.177 --dport 80 -j DNAT --to 192.168.4.177:8080
```
<!-- more -->
#### 本地连接的端口请求转发
本地连接指的是在本机上，用 127.0.0.1 或者本机 IP 来访问本机的端口。本地连接的数据包不会通过网卡，而是由内核处理后直接发给本地进程。这种数据包在 iptables 中只经过 OUTPUT 链，而不会经过 PREROUTING 链。所以需要在 OUTPUT 链中进行 DNAT。除了对 127.0.0.1 之外，对本机 IP (即 192.168.4.177) 的访问也属于本地连接
```
iptables -t nat -A OUTPUT -p tcp -d 127.0.0.1 --dport 80 -j DNAT --to 127.0.0.1:8080
iptables -t nat -A OUTPUT -p tcp -d 192.168.4.177 --dport 80 -j DNAT --to 127.0.0.1:8080

```

#### 注意事项
需要通过以下命令打开IP转发
```
echo 1 > /proc/sys/net/ipv4/ip_forward
sysctl
```
