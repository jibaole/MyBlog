---
title: mysql的权限管理
tags:
  - mysql
categories:
  - Database
toc: true
date: 2015-03-14 16:42:44
keywords:
  - mysql
  - 权限管理
description:
  - mysql
  - 权限管理
feature:
---

原则：禁止mysql可以被外网IP远程访问。即不允许下面这种情况：
``` shell
[root@VM_198_220_centos ~]# netstat -lnpt|grep 3306
tcp        0      0 0.0.0.0:3306              0.0.0.0:*                   LISTEN      16908/mysqld
正确为：
[root@VM_198_220_centos ~]# netstat -lnpt|grep 3306
tcp        0      0 127.0.0.1:3306              0.0.0.0:*                   LISTEN      16908/mysqld
```
<!-- more -->

### 一、MySQL禁外网IP远程访问的设置
1.在mysql的配置文件[mysqld]下面新增bind-address，如下：
```
[root@VM_198_220_centos ~]# vim /etc/my.cnf 
[mysqld]
datadir=/var/lib/mysql
socket=/var/lib/mysql/mysql.sock
user=mysql
bind-address=127.0.0.1
#skip-grant-tables
# Disabling symbolic-links is recommended to prevent assorted security risks
symbolic-links=0
default-character-set=utf8
[mysqld_safe]
log-error=/var/log/mysqld.log
pid-file=/var/run/mysqld/mysqld.pid
```

2.新建普通数据库用户，并对普通用户附加访问相关数据库的权限。方法如下：
例如：新建一个叫ymyj帐号，只允许其对数据库www_yumsaga_com进行操作。
命令如下：
```
GRANT ALL PRIVILEGES ON www_yumsaga_com.* TO ymyj@localhost IDENTIFIED BY 'ymyj-password' WITH GRANT OPTION;
或
GRANT select,insert,update,delete www_yumsaga_com.* TO ymyj@localhost IDENTIFIED BY 'ymyj-password' WITH GRANT OPTION;

```

### 二、MySQL常见问题处理
1.当我们遇到localhost或者127.0.0.1无法访问mysql的时候，可能是因为localhost和127.0.0.1的数据库访问权限被修改，可以通过如下方法来处理：
* 停止mysql
``` shell
[root@VM_198_220_centos ~]# /etc/init.d/mysqld stop

```
* 启动mysql安全模式：
``` shell
[root@VM_198_220_centos ~]#mysqld_safe --skip-grant-tables &
```

* 再打开一个终端，并执行如下命令：
``` shell
[root@VM_198_220_centos ~]#mysql
mysql> use mysql;
mysql> select * from user\G
注：通过上条命令可查询出Host为localhost或127.0.0.1的所有权限记录，如果相关权限值为N，则可以通过如下命令修改为Y来解决。
mysql> update user set xxx_priv='Y' where host='localhost/127.0.0.1' and user='root/其他用户';
update user set Select_priv='Y' where host='localhost' and user='rxyx';
update user set Insert_priv='Y' where host='localhost' and user='rxyx';
update user set Update_priv='Y' where host='localhost' and user='rxyx';
update user set Delete_priv='Y' where host='localhost' and user='rxyx';
-------------------------------------------------------------------------------
注：xxx_priv包括Select_priv、 Insert_priv、Update_priv、Delete_priv、Create_priv等。
```

2.通过命令行导入数据库后，发现数据库中有中文内容的表，数据都为乱码，解决办法：
``` sql
mysql -uroot -h 127.0.0.1 -pxxx --default-character-set=utf8 <xxx.sql
在命令行添加--default-character-set=对应字符集
```


### 三、MySQL权限相关知识
#### 1.MySQL权限简介
``` sql
-- 权限 权限级别 权限说明
CREATE 数据库、表或索引 创建数据库、表或索引权限
DROP 数据库或表 删除数据库或表权限
GRANT OPTION 数据库、表或保存的程序 赋予权限选项
REFERENCES 数据库或表
ALTER 表 更改表，比如添加字段、索引等
DELETE 表 删除数据权限
INDEX 表 索引权限
INSERT 表 插入权限
SELECT 表 查询权限
UPDATE 表 更新权限
CREATE VIEW 视图 创建视图权限
SHOW VIEW 视图 查看视图权限
ALTER ROUTINE 存储过程 更改存储过程权限
CREATE ROUTINE 存储过程 创建存储过程权限
EXECUTE 存储过程 执行存储过程权限
FILE 服务器主机上的文件访问 文件访问权限
CREATE TEMPORARY TABLES 服务器管理 创建临时表权限
LOCK TABLES 服务器管理 锁表权限
CREATE USER 服务器管理 创建用户权限
PROCESS 服务器管理 查看进程权限
RELOAD
服务器管理 执行flush-hosts, flush-logs, flush-privileges, flush-status, flush-tables, flush-threads, refresh, reload等命令的权限
REPLICATION CLIENT 服务器管理 复制权限
REPLICATION SLAVE 服务器管理 复制权限
SHOW DATABASES 服务器管理 查看数据库权限
SHUTDOWN 服务器管理 关闭数据库权限
SUPER 服务器管理 执行kill线程权限
```

#### 2.MYSQL的权限分布
权限分布 可能的设置的权限
``` sql
表权限 ‘Select’, ‘Insert’, ‘Update’, ‘Delete’, ‘Create’, ‘Drop’, ‘Grant’, ‘References’, ‘Index’, ‘Alter’
列权限 ‘Select’, ‘Insert’, ‘Update’, ‘References’
过程权限 ‘Execute’, ‘Alter Routine’, ‘Grant’
```
#### 3.MySQL权限经验原则
权限控制主要是出于安全因素，因此需要遵循一下几个经验原则：
* 只授予能满足需要的最小权限，防止用户干坏事。比如用户只是需要查询，那就只给select权限就可以了，不要给用户赋予update、insert或者delete权限。
* 创建用户的时候限制用户的登录主机，一般是限制成指定IP或者内网IP段。
* 初始化数据库的时候删除没有密码的用户。安装完数据库的时候会自动创建一些用户，这些用户默认没有密码。
* 为每个用户设置满足密码复杂度的密码。
* 定期清理不需要的用户。回收权限或者删除用户。

#### 4.MySQL权限实战
* GRANT命令使用说明
例如：创建一个只允许从本地登录的超级用户jack，并允许将权限赋予别的用户，密码为：jack。
``` sql
mysql> grant all privileges on *.* to jack@'localhost' identified by "jack" with grant option;
Query OK, 0 rows affected (0.01 sec)
    GRANT命令说明：
    ALL PRIVILEGES 是表示所有权限，你也可以使用select、update等权限。
    ON 用来指定权限针对哪些库和表。
    *.* 中前面的*号用来指定数据库名，后面的*号用来指定表名。
    TO 表示将权限赋予某个用户。
    jack@'localhost' 表示jack用户，@后面接限制的主机，可以是IP、IP段、域名以及%，%表示任何地方。注意：这里%有的版本不包括本地，以前碰到过给某个用户设置了%允许任何地方登录，但是在本地登录不了，这个和版本有关系，遇到这个问题再加一个localhost的用户就可以了。
    IDENTIFIED BY 指定用户的登录密码。
    WITH GRANT OPTION 这个选项表示该用户可以将自己拥有的权限授权给别人。注意：经常有人在创建操作用户的时候不指定WITH GRANT OPTION选项导致后来该用户不能使用GRANT命令创建用户或者给其它用户授权。

```

  注：可以使用GRANT重复给用户添加权限，权限叠加，比如你先给用户添加一个select权限，然后又给用户添加一个insert权限，那么该用户就同时拥有了select和insert权限。
* 刷新权限
使用这个命令使权限生效，尤其是你对那些权限表user、db、host等做了update或者delete更新的时候。以前遇到过使用grant后权限没有更新的情况，只要对权限做了更改就使用FLUSH PRIVILEGES命令来刷新权限。
``` sql
mysql> flush privileges;
Query OK, 0 rows affected (0.01 sec)

```

* 查看权限
  a.查看当前用户的权限
  ``` sql
  mysql> show grants;
  +---------------------------------------------------------------------+
  | Grants for root@localhost                                           |
  +---------------------------------------------------------------------+
  | GRANT ALL PRIVILEGES ON *.* TO 'root'@'localhost' WITH GRANT OPTION |
  | GRANT PROXY ON ''@'' TO 'root'@'localhost' WITH GRANT OPTION        |
  +---------------------------------------------------------------------+
  2 rows in set (0.00 sec)
  ```
  b.查看某个用户的权限
  ```
  mysql> show grants for 'jack'@'%';
  +-----------------------------------------------------------------------------------------------------+
  | Grants for jack@%                                                                                   |
  +-----------------------------------------------------------------------------------------------------+
  | GRANT USAGE ON *.* TO 'jack'@'%' IDENTIFIED BY PASSWORD '*9BCDC990E611B8D852EFAF1E3919AB6AC8C8A9F0' |
  +-----------------------------------------------------------------------------------------------------+
  1 row in set (0.00 sec)
  ```
* 回收权限
``` shell
mysql> revoke delete on *.* from 'jack'@'localhost';
Query OK, 0 rows affected (0.01 sec)
```
* 删除用户
``` shell
mysql> select host,user,password from user;
+-----------+------+-------------------------------------------+
| host      | user | password                                  |
+-----------+------+-------------------------------------------+
| localhost | root |                                           |
| rhel5.4   | root |                                           |
| 127.0.0.1 | root |                                           |
| ::1       | root |                                           |
| localhost |      |                                           |
| rhel5.4   |      |                                           |
| localhost | jack | *9BCDC990E611B8D852EFAF1E3919AB6AC8C8A9F0 |
+-----------+------+-------------------------------------------+
7 rows in set (0.00 sec)
mysql> drop user 'jack'@'localhost';
Query OK, 0 rows affected (0.01 sec)
```
* 对账户重命名
```
mysql> rename user 'jack'@'%' to 'jim'@'%';
Query OK, 0 rows affected (0.00 sec)
```
* 修改密码
  a.用set password命令
  ```
  mysql> SET PASSWORD FOR 'root'@'localhost' = PASSWORD('123456');
  Query OK, 0 rows affected (0.00 sec)
  ```
  b.用mysqladmin
  ```
  [root@rhel5 ~]# mysqladmin -uroot -p123456 password 1234abcd
  ```
  注：
  格式：`mysqladmin -u用户名 -p旧密码 password 新密码`
  c.用update直接编辑user表
  ```
  mysql> use mysql
  Reading table information for completion of table and column names
  You can turn off this feature to get a quicker startup with -A
  Database changed
  mysql> update user set PASSWORD = PASSWORD('1234abcd') where user = 'root';
  Query OK, 1 row affected (0.01 sec)
  Rows matched: 1  Changed: 1  Warnings: 0
  mysql> flush privileges;
  Query OK, 0 rows affected (0.00 sec)
  ```
  d.在丢失root密码的时候
  ```
  [root@rhel5 ~]# mysqld_safe --skip-grant-tables &
  [1] 15953
  [root@rhel5 ~]# 130911 09:35:33 mysqld_safe Logging to '/mysql/mysql5.5/data/rhel5.4.err'.
  130911 09:35:33 mysqld_safe Starting mysqld daemon with databases from /mysql/mysql5.5/data
  ```
  另开一个终端：
  ```
  [root@rhel5 ~]# mysql -u root
  Welcome to the MySQL monitor.  Commands end with ; or \g.
  Your MySQL connection id is 2
  Server version: 5.5.22 Source distribution
  Copyright (c) 2000, 2011, Oracle and/or its affiliates. All rights reserved.
  Oracle is a registered trademark of Oracle Corporation and/or its
  affiliates. Other names may be trademarks of their respective
  owners.
  Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
  mysql> \s
  --------------
  mysql  Ver 14.14 Distrib 5.5.22, for Linux (i686) using  EditLine wrapper
  Connection id:        2
  Current database:    
  Current user:        root@
  SSL:            Not in use
  Current pager:        stdout
  Using outfile:        ''
  Using delimiter:    ;
  Server version:        5.5.22 Source distribution
  Protocol version:    10
  Connection:        Localhost via UNIX socket
  Server characterset:    utf8
  Db     characterset:    utf8
  Client characterset:    utf8
  Conn.  characterset:    utf8
  UNIX socket:        /tmp/mysql.sock
  Uptime:            36 sec
  Threads: 1  Questions: 5  Slow queries: 0  Opens: 23  Flush tables: 1  Open tables: 18  Queries per second avg: 0.138
  --------------
  mysql> use mysql
  Reading table information for completion of table and column names
  You can turn off this feature to get a quicker startup with -A
  Database changed
  mysql> update user set password = PASSWORD('123456') where user = 'root';
  Query OK, 1 row affected (0.00 sec)
  Rows matched: 1  Changed: 1  Warnings: 0
  mysql> flush privileges;
  Query OK, 0 rows affected (0.00 sec)
  ```
