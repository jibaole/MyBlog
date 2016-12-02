---
title: mysql while循环嵌套
tags:
  - mysql
categories:
  - Database
toc: false
date: 2013-11-17 15:19:57
description: 转自：http://imysqldba.blog.51cto.com/1222376/595433
feature:
---

今天在写存储过程，想要测试一些东西，其中有一个表，需要插入100万用户，每个用户有40行记录，这里面有两个循环，想到了嵌套。
一般大家都知道for循环的嵌套，但是mysql存储过程中只有三种循环while、repeat、loop。因为一直使用while，所以就打算用while来做这件事情。结果发现，总是不能够循环，直插入一个用户的40行记录，非常无语。代码如下：
<!-- more -->
``` sql
DELIMITER $$ 
  
USE `qqfs_db_items`$$ 
 
DROP PROCEDURE IF EXISTS `qqsf_proc_InsertItemTest`$$ 
 
CREATE  PROCEDURE `qqsf_proc_InsertItemTest`( 
  i_PlayerCount INT) COMMENT '插入道具表测试' 
  BEGIN 
   DECLARE a INT DEFAULT 1; 
   DECLARE b TINYINT DEFAULT 1; 
   WHILE (a <= i_PlayerCount) DO 
     
    SET a = a + 1; 
    WHILE (b <=40) DO 
     INSERT INTO qqfs_tbl_items (AuthID,Slot,CID,GTID,Nums,Endtime,Flags,UseInfo) 
     VALUES 
     (a,b,0,0,1,NOW(),0,1); 
     SET b = b + 1; 
    END WHILE; 
       END WHILE; 
  END$$ 
  
 DELIMITER ;
```
后来用select a,select b，自定义查询断点发现循环是继续的。网上查了下，貌似没人说清楚。后来仔细一看，原来自己受了以前程序中for循环的束缚，以为b的值总是会在第二次循环时被重置，其实b的值在一次循环之后，就已经变为40了，所以，有两种解决方法，一个是想程序中for循环一样，将b的初始化放在a的循环中；另一种就是在a循环中，将b的值再次初始化为1.
问题得到解决，新代码如下：
``` sql
 DELIMITER $$ 
  
 USE `qqfs_db_items`$$ 
  
 DROP PROCEDURE IF EXISTS `qqsf_proc_InsertItemTest`$$ 
  
 CREATE DEFINER=`root`@`%` PROCEDURE `qqsf_proc_InsertItemTest`( 
  i_PlayerCount INT) COMMENT '插入道具表测试' 
  BEGIN 
   DECLARE a INT DEFAULT 1; 
   DECLARE b TINYINT DEFAULT 1; 
   WHILE (a <= i_PlayerCount) DO 
  -- repeat 
     
     
    SET a = a + 1; 
    -- select a; 
    WHILE (b <=40) DO 
     INSERT INTO qqfs_tbl_items (AuthID,Slot,CID,GTID,Nums,Endtime,Flags,UseInfo) 
     VALUES 
     (a,b,0,0,1,NOW(),0,1); 
     SET b = b + 1; 
     -- select b; 
    END WHILE; 
     
    SET b = 1; 
    -- select a; 
    -- until a >= i_PlayerCount 
   -- end repeat; 
    END WHILE; 
  END$$ 
  
 DELIMITER ;
```
包含了repeat的实现和我在排错中给a和b打的select断点。希望对