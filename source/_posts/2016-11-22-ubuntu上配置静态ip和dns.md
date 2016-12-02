---
title: ubuntu上配置静态ip和dns
tags:
categories:
  - Linux server
toc: true
date: 2016-11-22 11:38:22
description: 转自：http://www.linuxdiyf.com/linux/14252.html
feature:
---

#### 前言
装好Ubuntu 15.04之后这两天一直在折腾、`sudo apt-get update`老是失败、开始怀疑是源的问题、换过源之后还是、从网易源换到阿里源等等。就算更新完成之后、使用`sudo apt-get install xxx`依然卡在下载那一步、一直是0%、但是能ping通源的网址、并且通过浏览器也可以访问、各种搜索和论坛都未解决。
最后把焦点放在了最不引我注意的网络上、说他最不引我注意是因为我网络用的好好的、但是就是有时候百度的时候会404、所以想直接使用静态IP并且换一个指定的DNS试试。果不其然、网络原因！下面奉上静态IP配置方式和DNS设置方式（永久生效）。

#### 静态IP配置
Ubuntu 的网络配置信息放在 `/etc/network/interfaces`中，如果配置动态获取ip，则在上述文件中加入以下内容：
使用如下命令打开网络配置信息文件：
``` bash
sudo gedit /etc/network/interfaces
```
<!-- more -->
编辑文件（注意检察你的getway是否正确。）：
``` bash
auto eth0
iface eth0 inet static
address 192.168.8.119
netmask 255.255.255.0
gateway 192.168.8.1
```
配置完成之后重启网络：
``` bash
sudo /etc/init.d/networking restart
```
这时查看的就修改好的配置信息了。

#### 配置DNS
直接在/etc/resolv.conf文件中添加DNS，重启后，文件会被重写，原来配置的DNS会消失，所以我们在/etc/resolvconf/resolv.conf.d/目录下新建一个tail文件，填写需要的DNS服务器便可解决此问题。
在/etc/resolvconf/resolv.conf.d/目录下新建一个tail文件：
``` bash
sudo vi /etc/resolvconf/resolv.conf.d/tail
```
在文件中输入DNS：
`nameserver 223.5.5.5`  #阿里的DNS、这里我使用的是阿里的源、也可以修改成别的。
保存。
如果还是不能上网、重启一下机器。

#### 总结
未知的错误一直得不到解决是很打击人的、但是解决问题带来的愉悦也是无法言语的。选择了就坚持下去。