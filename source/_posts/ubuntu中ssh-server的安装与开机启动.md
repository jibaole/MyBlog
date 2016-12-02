---
title: ubuntu中ssh server的安装与开机启动
tags:
  - ubuntu
categories:
  - Linux server
description: 
toc: true
date: 2012-11-09 10:54:12
feature:
---

#### 安装openssh-server
```
$ sudo apt-get install openssh-server
```

#### 查看openssh-server是否启动
```
$ ps -e | grep ssh
```
<!-- more -->
#### 启动、停止和重启openssh-server的命令
```
/etc/init.d/ssh start
/etc/init.d/ssh stop
/etc/init.d/ssh restart
```


#### 配置openssh-server
这里可以配置SSH的服务端口等，例如：默认端口是22，可以自定义为其他端口号，如222，然后需要重启SSH服务。
```
vim /etc/ssh/sshd_config
```


#### 配置openssh-server开机自动启动
打开/etc/rc.local文件，在exit 0语句前加入：
```
/etc/init.d/ssh start
```

参考：http://www.2cto.com/os/201308/238160.html