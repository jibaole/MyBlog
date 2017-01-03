---
title: mysql5.6表自动分区实现
tags:
  - mysql
categories:
  - Database
toc: true
date: 2016-05-15 10:25:44
keywords:
  - mysql
  - 自动分区
description:
  - mysql
  - 自动分区
feature:
---
#### 一、环境
>MySQL5.6.29

#### 二、准备工作
开启event
``` sql
mysql> show variables like "%event_scheduler%";
+-----------------+-------+
| Variable_name   | Value |
+-----------------+-------+
| event_scheduler | ON    |
+-----------------+-------+
1 row in set (0.00 sec)
```
注:如果未开启，可以使用如下命令开启:
``` sql
SET GLOBAL event_scheduler = ON;
SET @@global.event_scheduler = ON;
SET GLOBAL event_scheduler = 1;
SET @@global.event_scheduler = 1;
```
<!-- more -->
#### 三、对现有表数据进行手工分区
1. 将原有的主键ID字段添加到组合唯一索引中(即将未分区前的主键添加到已有的唯一索引中)

2. 将主键调整与唯一索引一致
例如：
``` sql
ALTER TABLE razor_login  DROP PRIMARY KEY, 
ADD PRIMARY KEY (`ID`,`appId`,`chId`,`srvId`,`version`,`roleId`,`login_time`), LOCK=NONE;
```
  注意:mysql5.6一定要添加LOCK=NONE,否则删除主键将会报错。

3. 使用range方式对已存在的表进行分区

* 按日
``` sql
ALTER TABLE razor_task PARTITION BY RANGE COLUMNS(task_time) (
     PARTITION p20160516 VALUES LESS THAN ('2016-05-16 00:00:00'),
     PARTITION p20160517 VALUES LESS THAN ('2016-05-17 00:00:00'),
     PARTITION p20160518 VALUES LESS THAN ('2016-05-18 00:00:00'),
     PARTITION p20160519 VALUES LESS THAN ('2016-05-19 00:00:00'),
     PARTITION p20160520 VALUES LESS THAN ('2016-05-20 00:00:00'),
     PARTITION p20160521 VALUES LESS THAN ('2016-05-21 00:00:00'),
     PARTITION p20160522 VALUES LESS THAN ('2016-05-22 00:00:00'),
     PARTITION p20160523 VALUES LESS THAN ('2016-05-23 00:00:00'),
     PARTITION p20160524 VALUES LESS THAN ('2016-05-24 00:00:00'),
     PARTITION p20160525 VALUES LESS THAN ('2016-05-25 00:00:00'),
     PARTITION p20160526 VALUES LESS THAN ('2016-05-26 00:00:00'),
     PARTITION p20160527 VALUES LESS THAN ('2016-05-27 00:00:00')
);
```
* 按月
``` sql
ALTER TABLE razor_login PARTITION BY RANGE COLUMNS(login_time) (
     PARTITION Jan2016 VALUES LESS THAN ('2016-02-01 00:00:00'),
     PARTITION Feb2016 VALUES LESS THAN ('2016-03-01 00:00:00'),
     PARTITION Mar2016 VALUES LESS THAN ('2016-04-01 00:00:00'),
     PARTITION Apr2016 VALUES LESS THAN ('2016-05-01 00:00:00'),
     PARTITION May2016 VALUES LESS THAN ('2016-06-01 00:00:00')
);
```
#### 四、编写自动添加分区的存储过程
* 按日
``` sql
DROP PROCEDURE IF EXISTS SP_D_create_delete_partition;
CREATE PROCEDURE SP_D_create_delete_partition(IN dbname VARCHAR(32),
IN tablename VARCHAR(32),IN newPartValue DATETIME)
BEGIN
-- 定义存放动态sql字符串变量
DECLARE keepStmt VARCHAR(2000) DEFAULT @stmt;
-- 定义存放待删除分区名称变量
DECLARE partitionToDrop VARCHAR(64);

-- 查找表中的第一个分区,并将分区名赋给partitionToDrop
SELECT partition_name
INTO partitionToDrop
FROM INFORMATION_SCHEMA.PARTITIONS
WHERE table_schema=dbname
AND table_name=tablename
AND partition_ordinal_position=1;

-- 执行动态SQL删除第一个分区
SET @stmt = CONCAT('ALTER TABLE ',tablename,' DROP PARTITION ', partitionToDrop);
PREPARE pStmt FROM @stmt;
EXECUTE pStmt;
DEALLOCATE PREPARE pStmt;

-- 使用传入的日期作为分区条件新增一个分区
SET @stmt = CONCAT('ALTER TABLE ',tablename,' ADD PARTITION (PARTITION p', 
DATE_FORMAT(newPartValue + interval 2 DAY, '%Y%m%d'), ' VALUES LESS THAN (\'', 
DATE_FORMAT(newPartValue + interval 2 DAY, '%Y-%m-%d 00:00:00'),'\')); ');
PREPARE pStmt FROM @stmt;
EXECUTE pStmt;
DEALLOCATE PREPARE pStmt;

-- 重置变量
SET @stmt = keepStmt;
END;
```
* 按月
``` sql
DROP PROCEDURE IF EXISTS SP_M_create_partition;
DELIMITER ;;
-- dbname为数据库名称
-- tablename为表名
-- newPartValue为时间字段，该字段时间点决定了新增分区的时间截至时间以及分区名称
CREATE PROCEDURE SP_M_create_partition (IN dbname VARCHAR(32),
IN tablename VARCHAR(32),IN newPartValue DATETIME)
BEGIN
  -- 定义存放动态sql字符串变量
  DECLARE keepStmt VARCHAR(2000) DEFAULT @stmt;
  -- 定义存放待删除分区名称变量
  DECLARE partitionToDrop VARCHAR(64);
  -- 查找表中的第一个分区,并将分区名赋给partitionToDrop
  SELECT partition_name
    INTO partitionToDrop
    FROM INFORMATION_SCHEMA.PARTITIONS
    WHERE table_schema=dbname
    AND table_name=tablename
    AND partition_ordinal_position=1;
  
  -- 执行动态SQL删除第一个分区
  SET @stmt = CONCAT('ALTER TABLE ',tablename,' DROP PARTITION ',  partitionToDrop);
  PREPARE pStmt FROM @stmt;
  EXECUTE pStmt;
  DEALLOCATE PREPARE pStmt;
  -- 使用传入的日期作为分区条件新增一个分区
  SET @stmt = CONCAT('ALTER TABLE ',tablename,' ADD PARTITION (PARTITION ', 
  DATE_FORMAT(newPartValue + interval 2 MONTH, '%b%Y'), ' VALUES LESS THAN (TO_DAYS(\'',  
  DATE_FORMAT(newPartValue + interval 2 MONTH, '%Y-%m-01 00:00:00'),'\')))');
  PREPARE pStmt FROM @stmt;
  EXECUTE pStmt;
  DEALLOCATE PREPARE pStmt;
  -- 重置变量
  SET @stmt = keepStmt;
END;;
DELIMITER ;
```
#### 五、使用mysql event实现类似linux crontab周期执行效果
* 按日
``` sql
create event D_create_delete_partition 
on schedule every 1 day starts '2016-06-13 06:00:00'
on completion preserve enable
COMMENT 'Remove oldest partition and add a new one '
do
BEGIN
CALL SP_D_create_delete_partition('razor','razor_realtimeonlineusers',CURDATE());
CALL SP_D_create_delete_partition('razor','razor_levelupgrade',CURDATE());
CALL SP_D_create_delete_partition('razor','razor_viplevelup',CURDATE());
CALL SP_D_create_delete_partition('razor','razor_experiencevariation',CURDATE());
CALL SP_D_create_delete_partition('razor','razor_functioncount',CURDATE());
CALL SP_D_create_delete_partition('razor','razor_newuserguide',CURDATE());
CALL SP_D_create_delete_partition('razor','razor_propconsume',CURDATE());
CALL SP_D_create_delete_partition('razor','razor_propertyvariation',CURDATE());
CALL SP_D_create_delete_partition('razor','razor_propgain',CURDATE());
CALL SP_D_create_delete_partition('razor','razor_task',CURDATE());
CALL SP_D_create_delete_partition('razor','razor_tollgate',CURDATE());
CALL SP_D_create_delete_partition('razor','razor_errorcode',CURDATE());
CALL SP_D_create_delete_partition('razor','razor_newuserprogress',CURDATE());
END |
```
* 按月
``` sql
CREATE EVENT M_create_partition
    ON SCHEDULE EVERY 1 MONTH
    DISABLE ON SLAVE
    COMMENT 'Remove oldest partition and add a new one '
    DO 
    CALL SP_M_create_partition('razor','razor_login',NOW());
```
  注意:如果第三个参数直接使用now则表示从创建event时间为开始执行时间，以后每日、每月任务都是这个时刻执行。

#### 六、赋予mysql user event权限
``` sql
GRANT EVENT ON *.* TO razor@'%';
```
#### 七、主从需要注意的地方
ON COMPLETION PRESERVE ENABLE DO BEGIN
http://www.linuxidc.com/Linux/2012-11/74644.htm

#### 八、参考
http://dev.mysql.com/doc/refman/5.6/en/partitioning.html
http://anothermysqldba.blogspot.com/2014/04/mysql-partitions-timestamp-datetime.html

#### 九、补充
* 开启、关闭事件
``` sql
　-- 开启事件

　　ALTER EVENT event_test ON

　　COMPLETION PRESERVE ENABLE;

　-- 关闭事件

　　ALTER EVENT event_test ON

　　COMPLETION PRESERVE DISABLE;
```
* 每日、每月新增分区
``` sql
-- 每日
SET @stmt = CONCAT('ALTER TABLE ',tablename,' ADD PARTITION (PARTITION p', DATE_FORMAT(newPartValue + interval 2 DAY, '%Y%m%d'), ' VALUES LESS THAN (\'', DATE_FORMAT(newPartValue + interval 2 DAY, '%Y-%m-%d 00:00:00'),'\')); ');

-- 每月
SET @stmt = CONCAT('ALTER TABLE ',tablename,' ADD PARTITION (PARTITION ', DATE_FORMAT(newPartValue + interval 2 MONTH, '%b%Y'), ' VALUES LESS THAN (\'', DATE_FORMAT(newPartValue + interval 2 MONTH, '%Y-%m-01 00:00:00'),'\')); ');
```