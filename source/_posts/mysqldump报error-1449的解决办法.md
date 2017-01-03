---
title: mysqldump报error 1449的解决办法
tags:
  - mysql
categories:
  - Database
toc: false
date: 2015-11-16 17:27:55
keywords:
  - mysql
  - mysqldump
  - 1449
description:
  - mysql
  - mysqldump
  - 1449
feature:
---

当用mysqldump导出数据库时有时会出现问题，例如以下命令执行时：
``` bash
mysqldump -uroot -pPasswd DBName >/home/www/DB_Backup.sql
```
出错，显示：
```
Got error: 1449: The user specified as a definer ('test'@'192.168.1.%') does not exist when using LOCK TABLES
```
其实上面的报错信息中已经说的很明白了，就是test用户没有lock tables的权限 ，对该用户增加 lock tables的权限就行了。
``` sql
mysql> GRANT select, lock tables ON dbname.*  TO 'test'@'192.168.1.%'
mysql> flush privileges;
```
再重新mysqldump，一切OK 。
注：具体grant权限时，要根据错误提示的用户和主机进行授权 。