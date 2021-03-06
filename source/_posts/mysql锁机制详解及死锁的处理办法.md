---
title: mysql锁机制详解及死锁的处理办法
tags:
  - mysql
categories:
  - Database
toc: true
date: 2014-03-17 10:59:40
keywords:
  - mysql
  - 死锁
description:
  - mysql
  - 死锁
feature:
---

### 一、概述
MySQL有三种锁的级别：页级、表级、行级。
MyISAM和MEMORY存储引擎采用的是表级锁（table-level locking）；BDB存储引擎采用的是页面锁（page-level
locking），但也支持表级锁；InnoDB存储引擎既支持行级锁（row-level locking），也支持表级锁，但默认情况下是采用行级锁。
MySQL这3种锁的特性可大致归纳如下：
* 表级锁：开销小，加锁快；不会出现死锁；锁定粒度大，发生锁冲突的概率最高,并发度最低。
* 行级锁：开销大，加锁慢；会出现死锁；锁定粒度最小，发生锁冲突的概率最低,并发度也最高。
* 页面锁：开销和加锁时间界于表锁和行锁之间；会出现死锁；锁定粒度界于表锁和行锁之间，并发度一般。
<!-- more -->

### 二、MyISAM表锁
MyISAM存储引擎只支持表锁，是现在用得最多的存储引擎。
但表级锁让多线程可以同时从数据表中读取数据，但是如果另一个线程想要写数据的话，就必须要先取得排他访问。正在更新数据时，必须要等到更新完成了，其他线程才能访问这个表。（这种机制造成了并发读写容易出现表锁争夺而导致阻塞访问）

#### 1、查询表级锁争用情况
可以通过检查table_locks_waited和table_locks_immediate状态变量来分析系统上的表锁定争夺：
``` bash
mysql> show status like ‘table%’;
+———————–+———-+
| Variable_name | Value |
+———————–+———-+
| Table_locks_immediate | 76939364 | （表示可以立即获取锁的次数）
| Table_locks_waited | 305089 | （表示不能立即获取锁，需要等待锁的次数;）
+———————–+———-+
2 rows in set (0.00 sec)
```
Table_locks_waited/(Table_locks_immediate+Table_locks_waited)
这个比例值越大说明表级锁争用的情况越严重。
例：比例值=0.01说明100次进程里就有一次是需要等待锁的进程；

#### 2、MySQL表级锁的锁模式
MySQL的表级锁有两种模式：表共享读锁（Table Read Lock）和表独占写锁（Table Write
Lock）。MyISAM在执行查询语句（SELECT）前，会自动给涉及的所有表加读锁，在执行更新操作（UPDATE、DELETE、INSERT等）前，会自动给涉及的表加写锁。
所以对MyISAM表进行操作，会有以下情况：
a、对MyISAM表的读操作（加读锁），不会阻塞其他进程对同一表的读请求，但会阻塞对同一表的写请求。只有当读锁释放后，才会执行其它进程的写操作。
b、对MyISAM表的写操作（加写锁），会阻塞其他进程对同一表的读和写操作，只有当写锁释放后，才会执行其它进程的读写操作。

#### 3、并发插入
原则上数据表有一个读锁时，其它进程无法对此表进行更新操作，但在一定条件下，MyISAM表也支持查询和插入操作的并发进行。
MyISAM存储引擎有一个系统变量concurrent_insert，专门用以控制其并发插入的行为，其值分别可以为0、1或2。
a、当concurrent_insert设置为0时，不允许并发插入。
b、当concurrent_insert设置为1时，如果MyISAM表中没有空洞（即表的中间没有被删除的行），MyISAM允许在一个进程读表的同时，另一个进程从表尾插入记录。这也是MySQL的默认设置。
c、当concurrent_insert设置为2时，无论MyISAM表中有没有空洞，都允许在表尾并发插入记录。

#### 4、MyISAM的锁调度
由于MySQL认为写请求一般比读请求要重要，所以如果有读写请求同时进行的话，MYSQL将会优先执行写操作。这样MyISAM表在进行大量的更新操作时（特别是更新的字段中存在索引的情况下），会造成查询操作很难获得读锁，从而导致查询阻塞。
我们可以通过一些设置来调节MyISAM的调度行为：
a、通过指定启动参数low-priority-updates，使MyISAM引擎默认给予读请求以优先的权利。
b、通过执行命令SET LOW_PRIORITY_UPDATES=1，使该连接发出的更新请求优先级降低。
c、通过指定INSERT、UPDATE、DELETE语句的LOW_PRIORITY属性，降低该语句的优先级。
上面3种方法都是要么更新优先，要么查询优先的方法。这里要说明的就是，不要盲目的给mysql设置为读优先，因为一些需要长时间运行的查询操作，也会使写进程“饿死”。只有根据你的实际情况，来决定设置哪种操作优先。
这些方法还是没有从根本上同时解决查询和更新的问题。在一个有大数据量高并发表的mysql里，我们还可采用另一种策略来进行优化，那就是通过mysql主从（读写）分离来实现负载均衡，这样可避免优先哪一种操作从而可能导致另一种操作的堵塞。下面将用一个篇幅来说明mysql的读写分离技术。
MyISAM使用的是 flock 类的函数，直接就是对整个文件进行锁定（叫做文件锁定），InnoDB使用的是 fcntl 类的函数，可以对文件中局部数据进行锁定（叫做行锁定），所以区别就是在这里。
另外MyISAM的数据表是按照单个文件存储的，可以针对单个表文件进行锁定，但是InnoDB是一整个文件，把索引、数据、结构全部保存在 ibdata 文件里，所以必须用行锁定。
死锁
所谓死锁: 是指两个或两个以上的进程在执行过程中,
因争夺资源而造成的一种互相等待的现象,若无外力作用,它们都将无法推进下去.
此时称系统处于死锁状态或系统产生了死锁,这些永远在互相等竺的进程称为死锁进程.
表级锁不会产生死锁.所以解决死锁主要还是真对于最常用的InnoDB.
遇到死锁的处理方式
```
mysql -uxxx -pxxx -h服务器ip –port=服务器端口;（如果服务器设置了ip和端口访问的话，一定要带ip和端口）
mysql> show processlist; #查看正在执行的sql （show full processlist;查看全部sql）
mysql> kill id #杀死sql进程；
如果进程太多找不到，就重启mysql吧
/ect/init.d/mysql restart
或/ect/init.d/mysql stop（如果关不掉就直接kill -9 进程id）  再/ect/init.d/mysql start
```
去看看mysql日志文件是否保存死锁日志：
常用目录：/var/log/mysqld.log；（该目录还有其它相关日志文件就都看看）
怎么解决还是要看具体什么问题.