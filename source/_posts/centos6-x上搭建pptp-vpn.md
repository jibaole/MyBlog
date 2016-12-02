---
title: centos6.x上搭建pptp vpn
tags:
  - pptp
categories:
  - Network proxy
toc: true
date: 2015-09-11 11:01:08
description: 记录在centos6.x上面搭建pptp vpn的方法
feature:
---


### 一、准备工作
1.开放服务器pptpd服务对应访问权限。下图为aws云服务器的访问权限开发列表，pptpd服务涉及的端口为`tcp 1723`端口，如下图：

![](http://7xtlfa.com1.z0.glb.clouddn.com/static%2Fimages%2Faws1723.png)

2.如果是普通服务器，则可以通过iptalbes进行pptpd相关访问权限的配置。相关命令如下:
iptables的安装
```
yum –y install iptables
```

pptpd相关访问权限的开发
```
iptables -A INPUT -p tcp --dport 1723 -j ACCEPT
```

### 二、安装部署

#### 1.安装ppp
```
yum install ppp
```


#### 2.下载pptpd
下载地址：http://pan.baidu.com/s/1dD8dAzZ

#### 3.安装pptpd
rpm –ivh pptpd-1.4.0-3.el6.x86_64.rpm


#### 4.配置pptpd
编辑pptpd.conf
```
vi /etc/pptpd.conf

```

搜索localip，去掉下面字段前面的#，然后保存退出
```
localip 192.168.0.1
remoteip 192.168.0.2-238

```

编辑options.pptpd
```
vi /etc/ppp/options.pptpd

```

搜索ms-dns，去掉搜索到的两行ms-dns前面的#，并修改为下面的字段
```
ms-dns 8.8.8.8
ms-dns 8.8.4.4

```

接下来编辑/etc/ppp/chap-secrets设置VPN的帐号密码
```
vi /etc/ppp/chap-secrets

```

添加一行，按照下面的格式写入你的用户名和密码
```
用户名 pptpd 密码 *
# 注意：上面的用户名和密码都区分大小写
```

接下来修改内核参数，运行下面的命令编辑sysctl.conf
```
vi /etc/sysctl.conf

```

在conf末尾添加下面的代码，使内核支持转发
```
net.ipv4.ip_forward=1

```

运行下面的命令使内核修改生效
```
sysctl -p

```

添加下面的iptables转发规则（直接在SSH运行下面命令即可）
查看规则：`iptables -t nat -L`
* XEN架构
```
iptables -t nat -A POSTROUTING -s 192.168.0.0/24 -o eth0 -j MASQUERADE
```

* OpenVZ架构
```
iptables -t nat -A POSTROUTING -s 192.168.0.0/24 -j SNAT --to-source VPS公网IP

```

保存配置
```
service iptables save
```

#### 5.启动pptpd
用下面的命令启动pptpd
```
service pptpd start

```

用下面的命令使pptpd开机自动启动
```
chkconfig pptpd on

```

现在就可以使用你的设备连接到你的VPN了，若配置正确，就可以正常连接并上网了.
