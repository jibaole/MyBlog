---
title: mysql分组排序取前n条记录以及生成自动数字序列
tags:
  - mysql
categories:
  - Database
toc: false
date: 2013-11-17 10:36:55
description:
feature:
---

>同事提了一个需求，要求按照某列分组，然后将各组的前几条抽取出来。

表结构：
``` sql
CREATE TABLE `total_freq_ctrl` (  
   `time` int(10) unsigned NOT NULL,  
   `machine` char(64) NOT NULL,  
   `module` char(32) NOT NULL,  
   `total_flow` int(10) unsigned NOT NULL,  
   `deny_flow` int(10) unsigned NOT NULL,  
   PRIMARY KEY (`module`,`machine`,`time`)  
 ) ENGINE=InnoDB DEFAULT CHARSET=utf8  
```
<!-- more -->
``` sql
SELECT machine, deny_flow, total_flow, time FROM total_freq_ctrl A WHERE 1 > (SELECT COUNT(machine) FROM total_freq_ctrl WHERE machine = A.machine AND time > A.time) AND A.module = 'all' ORDER BY A.time desc;  
```

只要将 1 改成N就变成取每组的前N条，因为我极其不喜欢子查询，就改就尝试改称join 的方式。不过这里需要对所有数据进行排序才能确定每组的前N条，所以最佳优化也要全表扫描一次。

首先我要对表中数据进行排序，引入一个变量@row来做rownumber
``` sql
 set @row=0;set @mid='';SELECT module, machine, time, @row:=@row+1 rownum FROM total_freq_ctrl order by module,machine,time desc limit 10;   
 Query OK, 0 rows affected (0.00 sec)  
   
 Query OK, 0 rows affected (0.00 sec)  
   
 +--------+---------------+------------+--------+  
 | module | machine       | time       | rownum |  
 +--------+---------------+------------+--------+  
 | all    | 10.201.20.181 | 1409640060 |      1 |  
 | all    | 10.201.20.181 | 1409640000 |      2 |  
 | all    | 10.201.20.181 | 1409639940 |      3 |  
 | all    | 10.201.20.181 | 1409639880 |      4 |  
 | all    | 10.201.20.97  | 1409640060 |      5 |  
 | all    | 10.201.20.97  | 1409640000 |      6 |  
 | all    | 10.201.20.97  | 1409639940 |      7 |  
 | all    | 10.201.20.97  | 1409639880 |      8 |  
 | all    | 10.201.20.98  | 1409640060 |      9 |  
 | all    | 10.201.20.98  | 1409640000 |     10 |  
 +--------+---------------+------------+--------+  
 ```
rownumber已经出来了，再加一个@mid来进行分组

``` sql
 set @row=0;set @mid='';SELECT module, machine, time,case when @mid = machine then @row:=@row+1 else @row:=1 end rownum, @mid:=machine FROM total_freq_ctrl order by module,machine,time desc limit 20;  
 Query OK, 0 rows affected (0.00 sec)  
   
 Query OK, 0 rows affected (0.00 sec)  
   
 +--------+---------------+------------+--------+---------------+  
 | module | machine       | time       | rownum | @mid:=machine |  
 +--------+---------------+------------+--------+---------------+  
 | all    | 10.201.20.181 | 1409640180 |      1 | 10.201.20.181 |  
 | all    | 10.201.20.181 | 1409640120 |      2 | 10.201.20.181 |  
 | all    | 10.201.20.181 | 1409640060 |      3 | 10.201.20.181 |  
 | all    | 10.201.20.181 | 1409640000 |      4 | 10.201.20.181 |  
 | all    | 10.201.20.181 | 1409639940 |      5 | 10.201.20.181 |  
 | all    | 10.201.20.181 | 1409639880 |      6 | 10.201.20.181 |  
 | all    | 10.201.20.97  | 1409640180 |      1 | 10.201.20.97  |  
 | all    | 10.201.20.97  | 1409640120 |      2 | 10.201.20.97  |  
 | all    | 10.201.20.97  | 1409640060 |      3 | 10.201.20.97  |  
 | all    | 10.201.20.97  | 1409640000 |      4 | 10.201.20.97  |  
 | all    | 10.201.20.97  | 1409639940 |      5 | 10.201.20.97  |  
 | all    | 10.201.20.97  | 1409639880 |      6 | 10.201.20.97  |  
 | all    | 10.201.20.98  | 1409640180 |      1 | 10.201.20.98  |  
 | all    | 10.201.20.98  | 1409640120 |      2 | 10.201.20.98  |  
 | all    | 10.201.20.98  | 1409640060 |      3 | 10.201.20.98  |  
 | all    | 10.201.20.98  | 1409640000 |      4 | 10.201.20.98  |  
 | all    | 10.201.20.98  | 1409639940 |      5 | 10.201.20.98  |  
 | all    | 10.201.20.98  | 1409639880 |      6 | 10.201.20.98  |  
 +--------+---------------+------------+--------+---------------+  
 ```
好了，再外面加一层inner join 再对 rownumber 做限制 就可以拿到目标数据了。

``` sql
 set @row=0;set @mid='';select a.*,b.rownum from total_freq_ctrl a inner join (SELECT module, machine, time, case when @mid = machine then @row:=@row+1 else @row:=1 end rownum, @mid:=machine mid FROM total_freq_ctrl order by module,machine,time desc) b on b.module=a.module and b.machine=a.machine and b.time=a.time where b.rownum<5;  
 Query OK, 0 rows affected (0.00 sec)  
   
 Query OK, 0 rows affected (0.00 sec)  
   
 +------------+---------------+--------+------------+-----------+--------+  
 | time       | machine       | module | total_flow | deny_flow | rownum |  
 +------------+---------------+--------+------------+-----------+--------+  
 | 1409640360 | 10.201.20.181 | all    |      53937 |      6058 |      1 |  
 | 1409640300 | 10.201.20.181 | all    |      52588 |      5701 |      2 |  
 | 1409640240 | 10.201.20.181 | all    |      54254 |      5608 |      3 |  
 | 1409640180 | 10.201.20.181 | all    |      54684 |      5811 |      4 |  
 | 1409640360 | 10.201.20.97  | all    |      50679 |      5307 |      1 |  
 | 1409640300 | 10.201.20.97  | all    |      50472 |      5239 |      2 |  
 | 1409640240 | 10.201.20.97  | all    |      51586 |      5509 |      3 |  
 | 1409640180 | 10.201.20.97  | all    |      50794 |      5378 |      4 |  
 | 1409640360 | 10.201.20.98  | all    |      84747 |      5652 |      1 |  
 | 1409640300 | 10.201.20.98  | all    |      84506 |      5696 |      2 |  
 | 1409640240 | 10.201.20.98  | all    |      84982 |      5513 |      3 |  
 | 1409640180 | 10.201.20.98  | all    |      83997 |      5623 |      4 |  
 +------------+---------------+--------+------------+-----------+--------+  
```
有同学推荐了这个链接：http://www.xaprb.com/blog/2006/12/07/how-to-select-the-firstleastmax-row-per-group-in-sql/
