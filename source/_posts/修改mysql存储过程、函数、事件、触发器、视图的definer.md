---
title: 修改mysql存储过程、函数、事件、触发器、视图的definer
tags:
  - mysql
categories:
  - Database
toc: true
date: 2015-05-14 14:54:38
description:
feature:
---

#### 存储过程
``` sql

select definer from mysql.proc;
update mysql.proc set definer='billing@%';
```


#### 事件
``` sql
select DEFINER from mysql.EVENTS;
update mysql.EVENTS set definer='billing@%';
```

#### 视图
``` sql
select DEFINER from information_schema.VIEWS; 
select concat("alter DEFINER=`billing`@`%` SQL SECURITY DEFINER VIEW ",TABLE_SCHEMA,".",TABLE_NAME," as ",VIEW_DEFINITION,";") from information_schema.VIEWS where DEFINER<>'billing@%'; 
```

#### 触发器
``` sql
select DEFINER from information_schema.TRIGGERS;
drop trigger upc.t_trigger1;
```


#### 函数
``` sql
DELIMITER ;;
CREATE DEFINER=`billing`@`%` trigger upc.t_trigger1 before delete on upc.sys_sequences   for each row 
begin
insert into upc.sys_sequences_bak values(2,old.SEQUENCE_NAME,old.START_BY,old.INCREMENT_BY,old.LAST_NUMBER,old.JVM_STEP_BY,now());
end;;
DELIMITER ;
```

