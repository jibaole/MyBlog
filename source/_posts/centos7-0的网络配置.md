---
title: centos7.0的网络配置
categories:
  - Linux server
toc: true
date: 2016-10-08 11:35:54
tags:
description: 参考：http://simonhu.blog.51cto.com/196416/1588971
feature:
---

#### ifconfig不见了？
```
[root@centos1 ~]# ifconfig
-bash: ifconfig: command not found
```

#### 使用ip代替了ifconfig
```
ip  [选项]  操作对象{link|addr|route...}
# ip link show                # 显示网络接口信息
# ip link set eth0 upi           # 开启网卡
# ip link set eth0 down          # 关闭网卡
# ip link set eth0 promisc on      # 开启网卡的混合模式
# ip link set eth0 promisc offi     # 关闭网卡的混个模式
# ip link set eth0 txqueuelen 1200   # 设置网卡队列长度
# ip link set eth0 mtu 1400        # 设置网卡最大传输单元
# ip addr show                # 显示网卡IP信息
# ip addr add 192.168.0.1/24 dev eth0 # 设置eth0网卡IP地址192.168.0.1
# ip addr del 192.168.0.1/24 dev eth0 # 删除eth0网卡IP地址
# ip route list                 # 查看路由信息
# ip route add 192.168.4.0/24  via  192.168.0.254 dev eth0 # 设置192.168.4.0网段的网关为192.168.0.254,数据走eth0接口
# ip route add default via  192.168.0.254  dev eth0    # 设置默认网关为192.168.0.254
# ip route del 192.168.4.0/24      # 删除192.168.4.0网段的网关
# ip route del default          # 删除默认路由
```
<!-- more  -->
#### 配置固定IP
`vim /etc/sysconfig/network-scripts/ifcfg-enp2s0`
```
HWADDR=00:E0:69:01:6A:96
TYPE=Ethernet
#BOOTPROTO=dhcp
DEFROUTE=yes
PEERDNS=yes
PEERROUTES=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_PEERDNS=yes
IPV6_PEERROUTES=yes
IPV6_FAILURE_FATAL=no
NAME=enp2s0
UUID=5b0a7d76-1602-4e19-aee6-29f57618ca01
ONBOOT=yes
IPADDR0=172.8.1.211
PREFIX0=24
GATEWAY0=172.8.1.1
DNS1=172.8.1.1
```

#### 安装ifconfig
[root@centos1 ~]#yum install net-tools