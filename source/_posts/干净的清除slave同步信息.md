---
title: 干净的清除slave同步信息
tags:
  - mysql
categories:
  - Database
toc: true
date: 2015-11-14 14:41:11
description:
feature:
---

```
mysql> show slave status\G;
*************************** 1. row ***************************
               Slave_IO_State: Reconnecting after a failed master event read
                  Master_Host: xxx.xxx.xxx.xxx
                  Master_User: slavexxx
                  Master_Port: 3306
                Connect_Retry: 60
              Master_Log_File: mysql-bin.000004
          Read_Master_Log_Pos: 18841739
               Relay_Log_File: local-relay-bin.000022
                Relay_Log_Pos: 4799132
        Relay_Master_Log_File: mysql-bin.000004
             Slave_IO_Running: Connecting
            Slave_SQL_Running: Yes
              Replicate_Do_DB:
          Replicate_Ignore_DB:
           Replicate_Do_Table:
       Replicate_Ignore_Table:
      Replicate_Wild_Do_Table:
  Replicate_Wild_Ignore_Table:
                   Last_Errno: 0
                   Last_Error:
                 Skip_Counter: 0
          Exec_Master_Log_Pos: 18841739
              Relay_Log_Space: 5297368
              Until_Condition: None
               Until_Log_File:
                Until_Log_Pos: 0
           Master_SSL_Allowed: No
           Master_SSL_CA_File:
           Master_SSL_CA_Path:
              Master_SSL_Cert:
            Master_SSL_Cipher:
               Master_SSL_Key:
        Seconds_Behind_Master: NULL
Master_SSL_Verify_Server_Cert: No
                Last_IO_Errno: 2003
                Last_IO_Error: error reconnecting to master 'slavexxx@xxx.xxx.xxx.xxx:3306' - retry-time: 60  retries: 938
               Last_SQL_Errno: 0
               Last_SQL_Error:
  Replicate_Ignore_Server_Ids:
             Master_Server_Id: 1
                  Master_UUID: fa8d6da3-a285-11e2-a150-00265518b2bb
             Master_Info_File: /mysqldata/master.info
                    SQL_Delay: 0
          SQL_Remaining_Delay: NULL
      Slave_SQL_Running_State: Slave has read all relay log; waiting for the slave I/O thread to update it
           Master_Retry_Count: 86400
                  Master_Bind:
      Last_IO_Error_Timestamp: 131024 09:31:40
     Last_SQL_Error_Timestamp:
               Master_SSL_Crl:
           Master_SSL_Crlpath:
           Retrieved_Gtid_Set:
            Executed_Gtid_Set:
                Auto_Position: 0
1 row in set (0.00 sec)
ERROR:
No query specified
mysql> stop slave;
Query OK, 0 rows affected (0.06 sec)
mysql> reset slave;
Query OK, 0 rows affected (0.11 sec)
mysql> show slave status\G;
*************************** 1. row ***************************
               Slave_IO_State:
                  Master_Host: xxx.xxx.xxx.xxx
                  Master_User: slavexxx
                  Master_Port: 3306
                Connect_Retry: 60
              Master_Log_File:
          Read_Master_Log_Pos: 4
               Relay_Log_File: local-relay-bin.000001
                Relay_Log_Pos: 4
        Relay_Master_Log_File:
             Slave_IO_Running: No
            Slave_SQL_Running: No
              Replicate_Do_DB:
          Replicate_Ignore_DB:
           Replicate_Do_Table:
       Replicate_Ignore_Table:
      Replicate_Wild_Do_Table:
  Replicate_Wild_Ignore_Table:
                   Last_Errno: 0
                   Last_Error:
                 Skip_Counter: 0
          Exec_Master_Log_Pos: 0
              Relay_Log_Space: 143
              Until_Condition: None
               Until_Log_File:
                Until_Log_Pos: 0
           Master_SSL_Allowed: No
           Master_SSL_CA_File:
           Master_SSL_CA_Path:
              Master_SSL_Cert:
            Master_SSL_Cipher:
               Master_SSL_Key:
        Seconds_Behind_Master: NULL
Master_SSL_Verify_Server_Cert: No
                Last_IO_Errno: 0
                Last_IO_Error:
               Last_SQL_Errno: 0
               Last_SQL_Error:
  Replicate_Ignore_Server_Ids:
             Master_Server_Id: 1
                  Master_UUID: fa8d6da3-a285-11e2-a150-00265518b2bb
             Master_Info_File: /mysqldata/master.info
                    SQL_Delay: 0
          SQL_Remaining_Delay: NULL
      Slave_SQL_Running_State:
           Master_Retry_Count: 86400
                  Master_Bind:
      Last_IO_Error_Timestamp:
     Last_SQL_Error_Timestamp:
               Master_SSL_Crl:
           Master_SSL_Crlpath:
           Retrieved_Gtid_Set:
            Executed_Gtid_Set:
                Auto_Position: 0
1 row in set (0.00 sec)
ERROR:
No query specified
```
<!-- more -->
一些同步的信息还在，其实执行reset slave，其实是把master.info和relay-log.info文件给删除，但里面的同步信息还在，那么可以用这个方法，让其清除的彻彻底底。
```
mysql> reset slave all;
ERROR 2006 (HY000): MySQL server has gone away
No connection. Trying to reconnect...
Connection id:    1227
Current database: *** NONE ***
Query OK, 0 rows affected (0.00 sec)
mysql> show slave status\G;
Empty set (0.00 sec)
ERROR:
No query specified
mysql>
```
