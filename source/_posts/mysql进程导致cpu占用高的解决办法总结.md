---
title: mysql进程导致cpu占用高的解决办法总结
tags:
  - mysql
categories:
  - Database
toc: true
date: 2015-05-14 11:36:52
description:
feature:
---

#### 修改tmp_table_size的默认值
```
[mysqld]
tmp_table_size=200M #默认是32M
```

#### 调用 show processlist, 查看当前 mysql 使用频繁的 sql 语句，优化sql(索引)
```
mysql> show processlist;

```
注：此项可以配置慢查询进行确认.

#### ...