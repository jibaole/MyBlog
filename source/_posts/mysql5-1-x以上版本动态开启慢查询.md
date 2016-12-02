---
title: mysql5.1.x以上版本动态开启慢查询
tags:
  - mysql
categories:
  - Database
toc: false
date: 2013-11-17 16:30:03
description: 转自：http://blog.itpub.net/12309491/viewspace-683297
feature:
---

以MySQL 5.1.36为例：
在slow_query_log （注意log_slow_querys参数已经废弃）值为ON的情况下（默认为OFF），当一条SQL语句执行的时间超过了long_query_time 预设的时间（默认为10s，同时精确到微秒）时，默认（log_output值为FIFL时）就会把这种慢查询记录到：slow_query_log_file值所指定的文件中。
``` sql
mysql> select @@global.log_output;
+---------------------+
| @@global.log_output |
+---------------------+
| FILE                | 
+---------------------+
1 row in set (0.00 sec)
```
<!-- more -->
注意在MySQL5.1就开始支持把慢查询的日志记录放到mysq.slow_log中，但需要设置log_output变量值为TABLE：
``` sql
mysql> set @@global.log_output='TABLE';
Query OK, 0 rows affected (0.00 sec)
mysql> desc mysql.slow_log;
+----------------+------------------+------+-----+-------------------+-----------------------------+
| Field          | Type             | Null | Key | Default           | Extra                       |
+----------------+------------------+------+-----+-------------------+-----------------------------+
| start_time     | timestamp        | NO   |     | CURRENT_TIMESTAMP | on update CURRENT_TIMESTAMP | 
| user_host      | mediumtext       | NO   |     | NULL              |                             | 
| query_time     | time             | NO   |     | NULL              |                             | 
| lock_time      | time             | NO   |     | NULL              |                             | 
| rows_sent      | int(11)          | NO   |     | NULL              |                             | 
| rows_examined  | int(11)          | NO   |     | NULL              |                             | 
| db             | varchar(512)     | NO   |     | NULL              |                             | 
| last_insert_id | int(11)          | NO   |     | NULL              |                             | 
| insert_id      | int(11)          | NO   |     | NULL              |                             | 
| server_id      | int(10) unsigned | NO   |     | NULL              |                             | 
| sql_text       | mediumtext       | NO   |     | NULL              |                             | 
+----------------+------------------+------+-----+-------------------+-----------------------------+
11 rows in set (0.01 sec)
```
注意，文件记录和写表记录只会有一种生效。

修改之前：
``` sql
mysql> show variables like '%slow%';
+---------------------+------------------------------------------------+
| Variable_name       | Value                                          |
+---------------------+------------------------------------------------+
| log_slow_queries    | OFF                                            | 
| slow_launch_time    | 2                                              | 
| slow_query_log      | OFF                                            | 
| slow_query_log_file | /usr/local/mysql-5.1.36/var/localhost-slow.log | 
+---------------------+------------------------------------------------+
mysql> 
4 rows in set (0.00 sec)
```
注意这个时候localhost-slow.log文件是不存在的：
``` sql
mysql> system cat /usr/local/mysql-5.1.36/var/localhost-slow.log
cat: /usr/local/mysql-5.1.36/var/localhost-slow.log: No such file or directory
```
修改slow_query_log的方法：
``` sql
mysql> warnings;
Show warnings enabled.
mysql> set @@global.log_slow_queries=ON;
Query OK, 0 rows affected, 1 warning (0.01 sec)
Warning (Code 1287): The syntax '@@log_slow_queries' is deprecated and will be removed in MySQL 7.0. Please use '@@slow_query_log' instead
```
注意已经不再使用log_slow_queries参数，用slow_query_log替代，这样修改slow_query_log时，log_slow_queries也会被隐性地修改：
``` sql
mysql> set @@global.slow_query_log=ON;
Query OK, 0 rows affected (0.00 sec)
```
修改之后：
``` sql
mysql> show variables like '%slow%';
+---------------------+------------------------------------------------+
| Variable_name       | Value                                          |
+---------------------+------------------------------------------------+
| log_slow_queries    | ON                                             | 
| slow_launch_time    | 2                                              | 
| slow_query_log      | ON                                             | 
| slow_query_log_file | /usr/local/mysql-5.1.36/var/localhost-slow.log | 
+---------------------+------------------------------------------------+
4 rows in set (0.00 sec)
```
这时localhost-slow.log文件已经建立：
``` sql
mysql> system cat /usr/local/mysql-5.1.36/var/localhost-slow.log
/usr/local/mysql-5.1.36/libexec/mysqld, Version: 5.1.36-log (Source distribution). started with:
Tcp port: 3306  Unix socket: /tmp/mysql.sock
Time                 Id Command    Argument
```
分析时我们可以直接查询慢查询日志，也可以通过mysqldumpslow命令（推荐）来解析这个文件：
``` bash
[root@localhost var]# mysqldumpslow localhost-slow.log 
Reading mysql slow query log from localhost-slow.log
Count: 1  Time=0.00s (0s)  Lock=0.00s (0s)  Rows=0.0 (0), 0users@0hosts
```
我们可以通过sleep函数来做简单的测试：
如：select sleep(11);
回去测试