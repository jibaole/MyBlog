---
title: 在不重启apache的情况下使修改的配置生效
categories:
  - Web server
toc: true
date: 2016-11-08 14:05:42
tags:
  - apache
description: 
feature:
---
#### apache webserver支持的操作
start
restart
graceful
stop
graceful-stop

#### 关于graceful apache webserver restart
这种重启方式可以避免在父进程重启的情况下子进程跟着重启

#### gracefully restart apache webserver的语法
使用root用户执行如下命令：
```
apachectl -k graceful
```
OR
```
apache2ctl -k graceful
```
<!-- more  -->
也可以使用特定linux版本的初始化脚本来gracefully restart webserver，下面的命令只适用于RHEL/CentOS Linux：
```
/etc/init.d/httpd graceful
```
OR
```
/sbin/service httpd graceful
```
Debian / Ubuntu Linux：
```
/etc/init.d/apache2 reload
```

#### 监控apache进程
```
## For RHEL / CentOS and friends ##
tail -f /var/log/httpd/error_log
 
## For  Debian / Ubuntu and friends ##
tail -f /var/log/apache2/error.log
 
## FreeBSD Unix ##
tail -f /var/log/httpd-error.log
```

参考：http://www.cyberciti.biz/faq/apache-2-reload-httpd-config-file-unix-linux-command/
