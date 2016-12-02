---
title: ubuntu上安装和配置shadowsocks
tags:
  - shadowsocks
categories:
  - Network proxy
toc: true
date: 2015-11-15 17:26:25
description:
feature:
---

#### 安装Shadowsock
``` bash
$ sudo apt-getupdate
$ sudo apt-getinstallpython-geventpython-pip
$ sudo pip install shadowsocks
$ apt-getinstallpython-m2crypto
```
#### 配置Shadowsocks

1. 创建config.json配置文件
``` bash
mkdir -p /etc/shadowsocks
touch /etc/shadowsocks/config.json
```
2. 修改config.json
``` json
{
"server":"x.x.x.x",
"server_port":8388,
"local_port":1080,
"password":"xxx",
"timeout":600,
"method":"aes-256-cfb"
}
#x.x.x.x修改成vps的外网Ip,xxx修改后面客户端需要使用的密码
```
<!-- more -->
#### 运行Shadowsocks
``` bash
$ sudo su -
# 启动
ssserver -c /etc/shadowsocks/config.json -d start
# 停止 
ssserver -c /etc/shadowsocks/config.json -d stop
```
