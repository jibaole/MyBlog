---
title: 配置ssh使用密钥登录
tags:
  - ssh
categories:
  - Linux server
toc: true
date: 2013-11-15 14:46:46
description: 转自：http://blog.chinaunix.net/uid-8116903-id-334714.html
feature:
---

#### 配置私钥
1. 使用命令`ssh-keygen -t rsa`生成密钥，会生成一个私钥和一个公钥，在提示输入passphrase时如果不输入，直接回车，那么以后你登录服务器就不会验证密码，否则会要求你输入passphrase，默认会将私钥放在`/root/.ssh/id_rsa`公钥放在`/root/.ssh/id_rsa.pub`。

2. 将公钥拷贝到远程服务器上的`/root/.ssh/authorized_keys`文件
``` bash
scp /root/.ssh/id_rsa.pub server:/root/.ssh/authorized_keys
```
  注意：文件名一定要叫authorized_keys。

3. 客户端上保留私钥，公钥留不留都可以。也就是服务器上要有公钥，客户端上要有私钥。这样就可以实现无密码验证登录了。
<!-- more -->

#### 禁止口令登录
如果想要获得最大化的安全性，禁止口令登录，可以修改www.example.com上`/etc/ssh/sshd_conf`中的
`PasswordAuthentication yes` 改为
`PasswordAuthentication no`
也即只能使用密匙认证的openssh，禁止使用口令认证。

