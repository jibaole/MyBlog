---
title: ubuntu memcached服务端的安装
tags:
  - memcached
categories:
  - Database
toc: true
date: 2015-11-18 15:53:13
keywords:
  - ubuntu
  - memcached
description:
  - ubuntu
  - memcached
feature:
---

#### 安装完Memcache服务端
``` bash
sudo apt-get install memcached
```
#### 启动服务
``` bash
memcached -d -m 128 -p 11111 -u root
```
<!-- more -->
#### 启动参数
``` bash
-p 监听的端口
-l 连接的IP地址, 默认是本机
-d start 启动memcached服务
-d restart 重起memcached服务
-d stop|shutdown 关闭正在运行的memcached服务
-d install 安装memcached服务
-d uninstall 卸载memcached服务
-u 以的身份运行 (仅在以root运行的时候有效)
-m 最大内存使用，单位MB。默认64MB
-M 内存耗尽时返回错误，而不是删除项
-c 最大同时连接数，默认是1024
-f 块大小增长因子，默认是1.25-n 最小分配空间，key+value+flags默认是48
-h 显示帮助
```