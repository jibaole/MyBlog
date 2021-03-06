---
title: linux下ifcfg-eth0的配置
tags:
categories:
  - Linux server
toc: true
date: 2011-07-14 17:06:11
keywords:
  - linux
  - 网卡配置
  - ifcfg
description:
  - linux
  - 网卡配置
  - ifcfg
feature:
---

#### 配置文件
``` shell
[root@localhost ~]# cat /etc/sysconfig/network-scripts/ifcfg-eth0
# Intel Corporation 82545EM Gigabit Ethernet Controller (Copper)
TYPE=Ethernet      #网卡类型
DEVICE=eth0       #网卡接口名称
ONBOOT=yes          #系统启动时是否自动加载
BOOTPROTO=static    #启用地址协议 --static:静态协议 --bootp协议 --dhcp协议
IPADDR=192.168.1.11    #网卡IP地址
NETMASK=255.255.255.0  #网卡网络地址
GATEWAY=192.168.1.1     #网卡网关地址
DNS1=10.203.104.41     #网卡DNS地址
HWADDR=00:0C:29:13:5D:74 #网卡设备MAC地址
BROADCAST=192.168.1.255  #网卡广播地址
```
<!-- more -->
#### 重新导入ifcfg-eth0网络配置文件
```
[root@localhost ~]# /etc/init.d/network reload
Shutting down interface eth0:                            [ OK ]
Shutting down loopback interface:                        [ OK ]
Bringing up loopback interface:                          [ OK ]
Bringing up interface eth0:                              [ OK ]
```

#### 网卡接口关闭与激活
```
[root@localhost ~]# ifdown eth0  #关闭网络
[root@localhost ~]# ifup eth0    #启动网络

```

#### 网络服务启动与关闭
* 方法一
```
[root@localhost ~]# service network stop    #关闭网络服务
[root@localhost ~]# service network start  #启动网络服务
[root@localhost ~]# service network restart #重启网络服务
```
* 方法二
```
[root@localhost ~]# /etc/init.d/network stop
[root@localhost ~]# /etc/init.d/network start
[root@localhost ~]# /etc/init.d/network restart 网卡状态查询
[root@localhost ~]# service network status
Configured devices:
lo eth0
Currently active devices:
lo eth0
```

#### 临时配置网卡信息
```
[root@localhost ~]# ifconfig eth0 10.1.1.10 netmask 255.0.0.0
```

#### 查看网卡接口信息，默认列出所有接口
```
[root@localhost ~]# ifconfig
eth0      Link encap:Ethernet  HWaddr 00:0C:29:13:5D:74
          inet addr:192.168.1.11  Bcast:192.168.1.255  Mask:255.255.255.0
          inet6 addr: fe80::20c:29ff:fe13:5d74/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:413 errors:0 dropped:0 overruns:0 frame:0
          TX packets:572 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:47701 (46.5 KiB)  TX bytes:64842 (63.3 KiB)
          Base address:0x2000 Memory:d8920000-d8940000
lo        Link encap:Local Loopback
          inet addr:127.0.0.1  Mask:255.0.0.0
          inet6 addr: ::1/128 Scope:Host
          UP LOOPBACK RUNNING  MTU:16436  Metric:1
          RX packets:407 errors:0 dropped:0 overruns:0 frame:0
          TX packets:407 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0
          RX bytes:70759 (69.1 KiB)  TX bytes:70759 (69.1 KiB)
```


#### 查看当前路由及网关信息
```
[root@localhost ~]# netstat -r
Kernel IP routing table
Destination    Gateway        Genmask        Flags  MSS Window  irtt Iface
192.168.1.0    *              255.255.255.0  U        0 0          0 eth0
169.254.0.0    *              255.255.0.0    U        0 0          0 eth0
default        192.168.1.1    0.0.0.0        UG        0 0          0 eth0
```

