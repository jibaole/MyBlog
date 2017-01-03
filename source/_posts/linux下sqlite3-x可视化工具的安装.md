---
title: linux下sqlite3.x可视化工具的安装
tags:
  - sqlite
categories:
  - Database
toc: true
date: 2016-08-18 10:48:27
keywords:
  - linux
  - sqlite
  - 可视化工具
description:
  - linux
  - sqlite
  - 可视化工具
feature:
---

#### 1.介绍
sqlite3是linux上的小巧的数据库，一个文件就是一个数据库。
#### 2.安装
要安装sqlite3，可以在终端提示符后运行下列命令:
``` bash
sudo apt-get install sqlite3
```
检查版本
``` bash
sqlite3 -version
```
#### 3.测试
当前目录下建立test.db测试数据库文件
``` bash
sqlite3 test.db
```
查询信息
```
.database
```
退出
```
.exit
```
<!-- more -->
#### 4.图形界面（一）
可以选择sqlitebrowser程式（qt3）
``` bash
sudo apt-get install sqlitebrowser
```
启动图形界面可以在终端提示符后输入sqlitebrowser

#### 5.图形界面（二）
``` bash
sudo apt-get install sqliteman
```

转自：http://lieyan.lofter.com/post/225d54_b449fc