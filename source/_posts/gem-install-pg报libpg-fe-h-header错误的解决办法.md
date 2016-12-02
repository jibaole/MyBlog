---
title: gem install pg报libpg-fe.h header错误的解决办法
tags:
  - rails
categories:
  - Web development
toc: true
date: 2016-04-15 17:29:38
description:
feature:
---

#### 报错信息
>Can’t find the ‘libpq-fe.h header when trying to install pg gem

#### 解决办法

* Ubuntu
``` bash
sudo apt-get install libpq-dev
```
* RHEL systems
``` bash
yum install postgresql-devel
```
* Mac
``` bash
brew install postgresql
```
