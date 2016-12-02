---
title: centos上安装php5.6环境
tags:
  - php
categories:
  - Web development
toc: false
date: 2015-05-14 15:16:12
description:
feature:
---
CentOs 6.x
```
rpm -Uvh http://mirror.webtatic.com/yum/el6/latest.rpm
```
CentOs 7.X
```
rpm -Uvh https://mirror.webtatic.com/yum/el7/epel-release.rpm
```

安装php 5.6相关组件
```
yum install php56w.x86_64 php56w-cli.x86_64 php56w-common.x86_64 php56w-gd.x86_64 php56w-ldap.x86_64 php56w-mbstring.x86_64 php56w-mcrypt.x86_64 php56w-mysql.x86_64 php56w-pdo.x86_64
```

安装php-fpm 5.6
```
yum install php56w-fpm
```
