---
title: mysql的导出
tags:
  - mysql
categories:
  - Database
keywords:
  - mysql
  - 导出
description:
  - mysql
  - 导出
toc: true
date: 2014-11-08 16:12:14
feature:
---

#### 导出数据库
``` sql
mysqldump -h 127.0.0.1  -u name -p  dbname >name.sql
```
将127.0.0.1的数据库dbname导出到name.sql文件里面

#### 导出数据库的某张表
``` sql
mysqldump -h 127.0.0.1  -u username -ppassword  dbname tablename >name.sql
```
将ip 127.0.0.1上面的数据库dbname里面的tablename表导出到name.sql文件里面
<!-- more  -->
#### 导出数据库的某张表的符合某些条件的数据
``` sql
mysqldump -h 127.0.0.1  -u username -ppassword  dbname tablename  --where="id>10 limit 10000" >name.sql
```
将数据库dbname里面的tablename表里面的 id>10 的数据取出来100条，导出到name.sql文件里面


#### 导出数据库的某张表不锁表
``` sql
mysqldump -h 127.0.0.1  -u username -ppassword --single-transaction dbname  tablename>name.sql
```


#### 只导出数据库的某张表的表结构而不导出数据
``` sql
mysqldump -h 127.0.0.1  -u username -ppassword --single-transaction -d dbname  tablename>name.sql
```

#### 只导出数据库的某张表的数据而不导出结构
``` sql
mysqldump -h 127.0.0.1  -u username -ppassword --single-transaction -t dbname  tablename>name.sql
```

参考：http://blog.csdn.net/molong1208/article/details/50237495

