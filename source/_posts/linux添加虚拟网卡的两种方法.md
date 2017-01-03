---
title: linux添加虚拟网卡的两种方法
tags:
categories:
  - Linux server
keywords:
  - linux
  - 虚拟网卡
description:
  - linux
  - 虚拟网卡
toc: true
date: 2011-08-09 16:57:54
feature:
---

#### 1.临时添加

在eth4物理网卡上面添加一个eth4:1001的虚拟网卡，并指定IP地址为192.168.10.33
```
sudo ifconfig eth4:1001 192.168.10.33 up
```
如果要删除网卡，则可以执行下面的命令
```
sudo ifconfig eth4:1001 down
```
<!-- more -->
#### 2.修改网卡配置文件永久添加

在ubuntu下,网卡的配置文件是/etc/network/interfaces，通过增加如下内容来添加虚拟网卡：
```
sudo vim /etc/network/interfaces
```
添加如下内容：
```
auto eth4:1001
iface eth4:1001 inet static
address 192.168.10.33
netmask 255.255.255.0
#network 192.168.10.1
#broadcast 192.168.1.255
```
保存后,重启网卡：
```
sudo /etc/init.d/networking restart
```
此种配置方法的优点：重启服务器或者网卡配置不会丢失.
