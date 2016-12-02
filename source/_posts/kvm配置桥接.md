---
title: kvm配置桥接
tags:
  - kvm
categories:
  - Virtualization technology
toc: true
date: 2015-07-14 16:15:55
description:
feature:
---


#### 还原网络设置
``` shell
[root@master ~]# route delete default            //删除默认网关
[root@master ~]# route -n
Kernel IP routing table
Destination    Gateway        Genmask        Flags Metric Ref    Use Iface
192.168.3.0    0.0.0.0        255.255.255.0  U    0      0        0 br0
[root@master ~]# brctl show
bridge name        bridge id                STP enabled        interfaces
br0                8000.525400a7b756        no                            eth0
[root@master ~]# brctl  delif  br0    eth0    //断开/删除br0上的eth0设备
[root@master ~]#ifconfig br0 down        //让br0设备停止活动
[root@master ~]#brctl delbr  br0          //删除br0
root@master ~]# brctl show                    //查看br0是否依然存在
bridge name        bridge id                STP enabled        interfaces
[root@master ~]# ifconfig  eth0 192.168.3.176/24    //重新给eth0分配IP地址
root@master ~]# route add default gw 192.168.3.1  //重新加入默认网关
1234567891011 [root@master ~]# ip addr show
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 16436 qdisc noqueue state UNKNOWN
link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
inet 127.0.0.1/8 scope host lo
inet6 ::1/128 scope host
valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP qlen 1000
link/ether 52:54:00:a7:b7:56 brd ff:ff:ff:ff:ff:ff
inet 192.168.3.176/24 brd 192.168.3.255 scope global eth0
inet6 fe80::5054:ff:fea7:b756/64 scope link
valid_lft forever preferred_lft forever
——————————————————————————–

```

<!-- more -->
#### 通过配置文件配置桥接设备
``` shell
[root@master ~]# cat /etc/sysconfig/network-scripts/ifcfg-eth0
DEVICE=eth0
TYPE=Ethernet
ONBOOT=yes
BRIDGE=br0
[root@master ~]# cat /etc/sysconfig/network-scripts/ifcfg-br0
DEVICE=br0
TYPE=Bridge
ONBOOT=yes
BOOTPROTO=static
IPADDR=192.168.3.176
NETMASK=255.255.255.0
GATEWAY=192.168.3.1
```
参数详解：
```
ifcfg-eth0
Device              指定网卡设备名
TYPE                指定网卡的类型为以太网卡
ONBOOT        指定网卡是否开机启动，切记要设置为yes
BRIDGE            指定桥接设备，此处指定为br0设备
建议：以上变量（非变量值）均为大写。
ifcfg-br0
Device              指定网卡设备名
TYPE                指定网卡的类型为桥接
ONBOOT        指定网卡是否开机启动，切记要设置为yes
BOOTPROTO 指定网卡启动如何获取IP地址，设置静态
IPADDR          设置br0绑定的IP地址
NETMASK      设置子网掩码地址
GATEWAY      设置网关
```
建议： TYPE变量的变量值按照第一个字母大写，其余字母小写的原则，如Ethernet,Bridge。

#### 重启网络服务
``` shell
[root@master ~]# service    NetworkManager stop
[root@master ~]#chkconfig NetworkManager off
[root@master ~]# service network restart
[root@master ~]# ip addr show
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 16436 qdisc noqueue state UNKNOWN
link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
inet 127.0.0.1/8 scope host lo
inet6 ::1/128 scope host
valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP qlen 1000
link/ether 52:54:00:a7:b7:56 brd ff:ff:ff:ff:ff:ff
4: br0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UNKNOWN
link/ether 52:54:00:a7:b7:56 brd ff:ff:ff:ff:ff:ff
inet 192.168.3.176/24 brd 192.168.3.255 scope global br0
inet6 fe80::5054:ff:fea7:b756/64 scope link
valid_lft forever preferred_lft forever
```

#### 查看桥接设备信息
```
[root@master ~]# brctl  show
bridge name  bridge id    STP enabled  interfaces
br0        8000.525400a7b756    no        eth0
[root@master ~]# route
Kernel IP routing table
Destination    Gateway        Genmask        Flags Metric Ref    Use Iface
192.168.3.0    *              255.255.255.0  U    0      0        0 br0
link-local      *              255.255.0.0    U    1004  0        0 br0
default        192.168.3.1    0.0.0.0        UG    0      0        0 br0
```
由此，桥接设备br0已经配置成功，当创建虚拟机的时候选择虚拟网卡接口的时候选择br0即可
开始安装虚拟机，此时查看桥接设备br0上面的网卡连接情况：
```
[root@master ~]# brctl  show
bridge name      bridge id    STP enabled  interfaces
br0          8000.0025903afe42    no      eth0
vnet0
```
