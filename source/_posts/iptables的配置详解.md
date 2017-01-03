---
title: iptables的配置详解
tags:
categories:
  - Linux server
keywords:
  - iptables
description:
  - iptables
toc: true
date: 2014-06-09 16:50:11
feature:
---

#### 查看本机iptables防火墙策略的设置情况
```
[root@tp ~]# iptables -L -n
Chain INPUT (policy ACCEPT)
target       prot opt source                 destination
Chain FORWARD (policy ACCEPT)
target       prot opt source                 destination
Chain OUTPUT (policy ACCEPT)
target       prot opt source                 destination
Chain RH-Firewall-1-INPUT (0 references)
target       prot opt source                 destination
ACCEPT       all    —    0.0.0.0/0              0.0.0.0/0
ACCEPT       icmp —    0.0.0.0/0              0.0.0.0/0             icmp type 255
ACCEPT       esp    —    0.0.0.0/0              0.0.0.0/0
ACCEPT       ah     —    0.0.0.0/0              0.0.0.0/0
ACCEPT       udp    —    0.0.0.0/0              224.0.0.251           udp dpt:5353
ACCEPT       udp    —    0.0.0.0/0              0.0.0.0/0             udp dpt:631
ACCEPT       all    —    0.0.0.0/0              0.0.0.0/0             state RELATED,ESTABLISHED
ACCEPT       tcp    —    0.0.0.0/0              0.0.0.0/0             state NEW tcp dpt:22
ACCEPT       tcp    —    0.0.0.0/0              0.0.0.0/0             state NEW tcp dpt:80
ACCEPT       tcp    —    0.0.0.0/0              0.0.0.0/0             state NEW tcp dpt:25
REJECT       all    —    0.0.0.0/0              0.0.0.0/0             reject-with icmp-host-prohibited
```
可以看出开启了防火墙。
<!-- more -->
如果防火墙策略未开启，则像如下这样：
```
[root@tp ~]# iptables -L -n
Chain INPUT (policy ACCEPT)
target       prot opt source                 destination
Chain FORWARD (policy ACCEPT)
target       prot opt source                 destination
Chain OUTPUT (policy ACCEPT)
target       prot opt source                 destination
```
清除原有的iptables防火墙策略
```
[root@tp ~]# iptables -F        #清除预设表filter中的所有规则链的规则
[root@tp ~]# iptables -X        #清除预设表filter中使用者自定链中的规则
```
检查一下，如果下面这样说明防火墙策略已经被清除了：
```
[root@tp ~]# iptables -L -n
Chain INPUT (policy ACCEPT)
target       prot opt source                 destination
Chain FORWARD (policy ACCEPT)
target       prot opt source                 destination
Chain OUTPUT (policy ACCEPT)
target       prot opt source                 destination
```
保存防火墙策略并使策略生效
```
[root@tp ~]# /etc/rc.d/init.d/iptables save
[root@tp ~]# service iptables restart
```
设定预设策略
```
[root@tp ~]# iptables -P INPUT DROP #谨慎操作，设置之后就无法远程ssh
[root@tp ~]# iptables -P OUTPUT ACCEPT
[root@tp ~]# iptables -P FORWARD DROP
```
 当超出了iptables里filter表里的两个链规则(INPUT,FORWARD)时,不在这两个规则里的数据包都DROP(放弃);
对于OUTPUT链,也就是流出的包我们不用做太多限制,而是采取ACCEPT。
#### 添加input规则

1. 允许ssh远程登录
```
[root@tp ~]# iptables -A INPUT -p tcp –dport 22 -j ACCEPT
# -A表示添加一条规则 -p表示协议  –dport 目的端口 –sport 源端口  -j 表示 这条规则的目的
```
2. 如果做邮件服务器，需要开启25、110端口
```
[root@tp ~]# iptables -A INPUT -p tcp –dport 110 -j ACCEPT
[root@tp ~]# iptables -A INPUT -p tcp –dport 25 -j ACCEPT
```
3. 如果做了FTP服务器,开启21端口
```
[root@tp ~]# iptables -A INPUT -p tcp –dport 21 -j ACCEPT
[root@tp ~]# iptables -A INPUT -p tcp –dport 20 -j ACCEPT
```
4. 如果做了DNS服务器,开启53端口
```
[root@tp ~]# iptables -A INPUT -p tcp –dport 53 -j ACCEPT
```
5. 允许icmp包通过,也就是允许ping
```
[root@tp ~]# iptables -A INPUT -p icmp -j ACCEPT    (INPUT设置成DROP的话)
```
6. 允许loopback!(不然会导致DNS无法正常关闭等问题)
```
IPTABLES -A INPUT -i lo -p all -j ACCEPT (如果是INPUT DROP)
```
7. 只允许192.168.0.3的机器进行SSH连接
```
[root@tp ~]# iptables -A INPUT -s 192.168.0.3 -p tcp –dport 22 -j ACCEPT
```

#### 添加forward规则

1. 开启转发功能时，需要配置FORWARD策略
```
[root@tp ~]# iptables -A FORWARD -i eth0 -o eth1 -m state –state RELATED,ESTABLISHED -j ACCEPT
[root@tp ~]# iptables -A FORWARD -i eth1 -o eh0 -j ACCEPT
```
2. 丢弃坏的TCP包
```
[root@tp ~]#iptables -A FORWARD -p TCP ! –syn -m state –state NEW -j DROP
```

3. 处理IP碎片数量,防止攻击,允许每秒100个
```
[root@tp ~]#iptables -A FORWARD -f -m limit –limit 100/s –limit-burst 100 -j ACCEPT
```
4. 设置ICMP包过滤,允许每秒1个包,限制触发条件是10个包.
```
[root@tp ~]#iptables -A FORWARD -p icmp -m limit –limit 1/s –limit-burst 10 -j ACCEPT
```

#### 配置一个nat表放火墙

1. 查看本机关于NAT的设置情况
```
[root@tp rc.d]# iptables -t nat -L
Chain PREROUTING (policy ACCEPT)
target       prot opt source                 destination
Chain POSTROUTING (policy ACCEPT)
target       prot opt source                 destination
SNAT         all    —    192.168.0.0/24         anywhere              to:211.101.46.235
Chain OUTPUT (policy ACCEPT)
target       prot opt source                 destination
```
2. 清除nat规则
```
[root@tp ~]# iptables -F -t nat
[root@tp ~]# iptables -X -t nat
[root@tp ~]# iptables -Z -t nat
```
3. 添加规则-防止外网用内网IP欺骗
```
[root@tp sysconfig]# iptables -t nat -A PREROUTING -i eth0 -s 10.0.0.0/8 -j DROP
[root@tp sysconfig]# iptables -t nat -A PREROUTING -i eth0 -s 172.16.0.0/12 -j DROP
[root@tp sysconfig]# iptables -t nat -A PREROUTING -i eth0 -s 192.168.0.0/16 -j DROP
```
4. 禁止与211.101.46.253的所有连接
```
[root@tp ~]# iptables -t nat -A PREROUTING    -d 211.101.46.253 -j DROP
```
5. 禁用FTP(21)端口
```
[root@tp ~]# iptables -t nat -A PREROUTING -p tcp –dport 21 -j DROP
```
6. drop非法连接
```
[root@tp ~]# iptables -A INPUT     -m state –state INVALID -j DROP
[root@tp ~]# iptables -A OUTPUT    -m state –state INVALID -j DROP
[root@tp ~]# iptables-A FORWARD -m state –state INVALID -j DROP
```
7. 允许所有已经建立的和相关的连接
```
[root@tp ~]# iptables-A INPUT -m state –state ESTABLISHED,RELATED -j ACCEPT
[root@tp ~]# iptables-A OUTPUT -m state –state ESTABLISHED,RELATED -j ACCEPT
[root@tp ~]# /etc/rc.d/init.d/iptables save
```
