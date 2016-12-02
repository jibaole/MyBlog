---
title: securecrt通过密钥ssh访问服务器
tags:
categories:
  - Linux server
toc: true
date: 2014-11-16 16:32:40
description:
feature:
---

>说明：
一般的密码方式登录容易被密码暴力破解。所以一般我们会将 SSH 的端口设置为默认22以外的端口，或者禁用root账户登录。其实可以通过密钥登录这种方式来更好地保证安全。
密钥形式登录的原理是：利用密钥生成器制作一对密钥——一只公钥和一只私钥。将公钥添加到服务器的某个账户上，然后在客户端利用私钥即可完成认证并登录。这样一来，没有私钥，任何人都无法通过 SSH 暴力破解你的密码来远程登录到系统。此外，如果将公钥复制到其他账户甚至主机，利用私钥也可以登录。
下面来讲解如何在 Linux 服务器上制作密钥对，将公钥添加给账户，设置 SSH，最后通过客户端登录。

#### 1. 制作密钥对
首先在服务器上制作密钥对。首先用密码登录到你打算使用密钥登录的账户，然后执行以下命令：
``` bash
[root@host ~]$ ssh-keygen  //建立密钥对Generating public/private rsa key pair.
Enter file in which to save the key (/root/.ssh/id_rsa)://按 Enter
Created directory '/root/.ssh'.
Enter passphrase (empty for no passphrase): //输入密钥锁码，或直接按 Enter 留空
Enter same passphrase again: //再输入一遍密钥锁码
Your identification has been saved in /root/.ssh/id_rsa. //私钥
Your public key has been saved in /root/.ssh/id_rsa.pub. //公钥
The key fingerprint is:0f:d3:e7:1a:1c:bd:5c:03:f1:19:f1:22:df:9b:cc:08 root@host
//密钥锁码在使用私钥时必须输入，这样就可以保护私钥不被盗用。当然，也可以留空，实现无密码登录。
//现在，在 root 用户的家目录中生成了一个 .ssh 的隐藏目录，内含两个密钥文件。id_rsa 为私钥，id_rsa.pub 为公钥。
```


#### 2. 在服务器上安装公钥
键入以下命令，在服务器上安装公钥：
``` bash
[root@host ~]$ cd .ssh
[root@host .ssh]$ cat id_rsa.pub >> authorized_keys
```
如此便完成了公钥的安装。为了确保连接成功，请保证以下文件权限正确：
``` bash
[root@host .ssh]$ chmod 600 authorized_keys
[root@host .ssh]$ chmod 700 ~/.ssh
```

#### 3. 设置 SSH，打开密钥登录功能
编辑 /etc/ssh/sshd_config 文件，进行如下设置：
```
RSAAuthentication yes
PubkeyAuthentication yes
```
另外，请留意 root 用户能否通过 SSH 登录，默认为yes：PermitRootLogin yes

当我们完成全部设置并以密钥方式登录成功后，可以禁用密码登录。这里我们先不禁用，先允许密码登陆PasswordAuthentication yes

最后，重启 SSH 服务：
``` bash
[root@host .ssh]$ service sshd restart
```


#### 4. 将私钥下载到客户端，如这里的SecureCRT
输入Hostname,Username，选择认证方式为PublicKey公钥认证，选择刚刚下载下来的id_rsa私钥文件即可！
![](http://static.coolnull.com/wp-content/uploads/2014/11/securecrt1.jpg)
