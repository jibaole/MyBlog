---
title: centos7.x关闭默认防火墙启用iptables防火墙
tags:
  - centos
categories:
  - Linux server
toc: true
date: 2016-05-11 11:36:06
keywords:
  - centos7
  - 防火墙
  - iptables
description:
  - centos7
  - 防火墙
  - iptables
feature: 
---

#### 关闭默认防火墙firewall
```

systemctl stop firewalld.service #停止firewall
systemctl disable firewalld.service #禁止firewall开机启动
firewall-cmd --state #查看默认防火墙状态（关闭后显示notrunning，开启后显示running）
```
<!-- more -->
#### 启用iptables防火墙
默认iptables已经安装，这里只需要做如下配置即可：
```
vi /etc/sysconfig/iptables #编辑防火墙配置文件
# sampleconfiguration for iptables service
# you can edit thismanually or use system-config-firewall
# please do not askus to add additional ports/services to this default configuration
*filter
:INPUT ACCEPT [0:0]
:FORWARD ACCEPT[0:0]
:OUTPUT ACCEPT[0:0]
-A INPUT -m state--state RELATED,ESTABLISHED -j ACCEPT
-A INPUT -p icmp -jACCEPT
-A INPUT -i lo -jACCEPT
-A INPUT -p tcp -mstate --state NEW -m tcp --dport 22 -j ACCEPT
-A INPUT -p tcp -m state --state NEW -m tcp --dport 80 -jACCEPT
-A INPUT -p tcp -m state --state NEW -m tcp --dport 8080 -j ACCEPT
-A INPUT -j REJECT--reject-with icmp-host-prohibited
-A FORWARD -jREJECT --reject-with icmp-host-prohibited
COMMIT
:wq! #保存退出
```

使iptalbes生效并设置开机启动：
```
systemctlrestart iptables.service #最后重启防火墙使配置生效
systemctlenable iptables.service #设置防火墙开机启动


```
