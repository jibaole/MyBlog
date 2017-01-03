---
title: 在centos6.x上安装shadowsocks服务端
tags:
  - shadowsocks
categories:
  - Network proxy
description: 
  - centos
  - shadowsocks
  - 安装
keywords:
  - centos
  - shadowsocks
  - 安装
toc: true
date: 2016-10-08 16:19:09
feature:
---

#### 查看系统
``` shell
[root@localhost ~]# cat /etc/issue 
CentOS release 6.6 (Final) 
[root@localhost ~]# uname -a 
Linux localhost.localdomain 2.6.32-042stab106.6 #1 SMP Mon Apr 20 14:48:47 MSK 2015 x86_64 x86_64 x86_64 GNU/Linux
```

#### 安装ShadowSocks
``` shell
# yum install python-setuptools && easy_install pip 
# pip install shadowsocks
```

<!-- more  -->
#### 创建配置文件/etc/shadowsocks.json
``` shell
[root@localhost /]# touch /etc/shadowsocks.json 
[root@localhost /]# vi /etc/shadowsocks.json 
{ 
  "server":"138.128.208.158", 
  "server_port":443, 
  "local_address": "127.0.0.1", 
  "local_port":1080, 
  "password":"MyPass", 
  "timeout":600, 
  "method":"rc4-md5"
}
```
>备注：加密方式官方默认使用aes-256-cfb，推荐使用rc4-md5，因为 RC4比AES速度快好几倍。
字段说明：
    `server`:服务器IP
    `server_port`:服务器端口
    `local_port`:本地端端口
    `password`:用来加密的密码
    `timeout`:超时时间（秒）
    `method`:加密方法，可选择 “bf-cfb”, “aes-256-cfb”, “des-cfb”, “rc4″等


#### 使用配置文件在后台运行shadowsocks服务
``` shell
[root@localhost /]# ssserver -c /etc/shadowsocks.json -d start
```

#### 停止服务
``` shell
[root@localhost /]# ssserver -c /etc/shadowsocks.json -d stop
```

参考：http://www.linuxidc.com/Linux/2015-05/117290.htm
