---
title: 在ubuntu上面安装pptp vpn
tags:
  - pptp
categories:
  - Network proxy
keywords:
  - pptp
  - vpn
  - ubuntu
description:
  - pptp
  - vpn
  - ubuntu
toc: true
date: 2016-12-28 15:16:52
thumbnail:
banner:
---

#### 安装pptpd
``` bash
$ sudo apt-get install pptpd
```

#### 编辑配置文件
```
sudo vi /etc/pptpd.conf
```
找到最下面，修改ip：
```
#localip 你的主机ip
remoteip 10.100.0.2-10
```
第二行为分配的ip段 
<!-- more -->
#### 设置dns
``` bash
$ sudo vi /etc/ppp/pptpd-options

```
修改以下部分为google的dns：
```
ms-dns 8.8.8.8
ms-dns 8.8.4.4
```

#### 设置账号
``` bash
$ sudo vi /etc/ppp/chap-secrets
```
添加一行，依次为：用户名，服务，密码，限制ip：
```
"user" pptpd "user" *
```

#### 重启服务
``` bash
$ sudo /etc/init.d/pptpd restart
```

#### 设置IP转发

```
$ sudo vi /etc/sysctl.conf
```
去掉文件中这一行的注释：
```
net.ipv4.ip_forward=1
```
使它立刻生效：
```
sudo sysctl -p
```
安装iptables，如果你还没有安装的话：
``` bash
sudo apt-get install iptables
```
建立一个 NAT：
``` bash
sudo iptables -t nat -A POSTROUTING -s 10.100.0.0/24-o eth0 -j MASQUERADE
```
将规则保存，使重启后规则不丢失：
``` bash
sudo iptables-save >/etc/iptables-rules
```
若此处提示：-bash: /etc/iptables-rules: Permission denied 则可使用root用户，命令：su - 进入root用户保存
编辑网卡文件，加载网卡时自动加载规则
``` bash
sudo vi  /etc/network/interfaces
```
末尾加入：
``` bash
pre-up iptables-restore </etc/iptables-rules
```
设置MTU，防止包过大：
``` bash
sudo iptables -A FORWARD -s 10.100.0.0/24-p tcp -m tcp --tcp-flags SYN,RST SYN -j TCPMSS --set-mss 1200
```
若设置了上条规则，记得保存：
``` bash
sudo iptables-save >/etc/iptables-rules
```
macos 能连上，但没有任何收发包的问题，即能连接上VPN，不能上网：vpn高级里勾选发送全部流量

至此，搞定。

转自：https://linux.cn/article-3376-1.html