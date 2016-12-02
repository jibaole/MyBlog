---
title: centos下安装apxs
tags:
  - apache
categories:
  - Web server
toc: true
date: 2015-04-16 16:26:23
description: 
feature:
---

>apxs 是Apache的扩展工具，用于编译一个或多个源程序或目标代码文件为动态共享对象，使之可以用LoadModule指令在运行时加载到Apache服器中。在CentOS下，httpd就是Apache，CentOS将apxs放在httpd-devel下的。

#### 查看软件包
``` bash
tmp]# yum list | grep httpd
httpd.i686 2.2.15-30.el6.centos @updates
httpd-tools.i686 2.2.15-30.el6.centos @updates
httpd-devel.i686 2.2.15-30.el6.centos updates
httpd-itk.i686 2.2.22-6.el6 epel
httpd-manual.noarch 2.2.15-30.el6.centos updates
iipsrv-httpd-fcgi.noarch 1.0.0-0.7.git0b63de7.el6 epel
libmicrohttpd.i686 0.9.22-1.el6 epel

```

<!-- more -->
#### 安装
``` bash
tmp]# yum install -y httpd-devel
Loaded plugins: priorities
64 packages excluded due to repository priority protections
Setting up Install Process
Resolving Dependencies
--> Running transaction check
---> Package httpd-devel.i686 0:2.2.15-30.el6.centos will be installed

```

#### 测试
``` bash
tmp]# apxs
Usage: apxs -g [-S <var>=<val>] -n <modname>
apxs -q [-S <var>=<val>] <query> ...
apxs -c [-S <var>=<val>] [-o <dsofile>] [-D <name>[=<value>]]
[-I <incdir>] [-L <libdir>] [-l <libname>] [-Wc,<flags>]
[-Wl,<flags>] [-p] <files> ...
apxs -i [-S <var>=<val>] [-a] [-A] [-n <modname>] <dsofile> ...
apxs -e [-S <var>=<val>] [-a] [-A] [-n <modname>] <dsofile> ...
```

转自：http://hi.baidu.com/wj745168/item/5614dff0ba43cb59c8f337ff