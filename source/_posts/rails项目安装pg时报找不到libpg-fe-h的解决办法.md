---
title: rails项目安装pg时报找不到libpg-fe.h的解决办法
categories:
  - Web development
toc: true
date: 2016-07-08 15:19:30
tags:
  - rails
description: 参考：http://stackoverflow.com/questions/13086073/rails-install-pg-cant-find-the-libpq-fe-h-header
feature:
---


#### Mac
```
brew install postgresql
```

#### Ubuntu
```
sudo apt-get install libpq-dev
```

#### RHEL
```
yum install postgresql-devel
```