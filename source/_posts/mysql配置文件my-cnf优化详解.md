---
title: mysql配置文件my.cnf优化详解
tags:
  - mysql
categories:
  - Database
toc: true
date: 2015-07-14 16:40:45
keywords:
  - mysql
  - my.cnf
description:
  - mysql
  - my.cnf
feature:
---
my.cnf
``` sql
[client]
character-set-server = utf8
port    = 3306
socket  = /data/mysql/3306/mysql.sock
 
[mysqld]
character-set-server = utf8
 
user    = mysql
port    = 3306
socket  = /data/mysql/3306/mysql.sock
basedir = /usr/local/webserver/mysql
datadir = /data/mysql/3306/data
log-error = /data/mysql/3306/mysql_error.log
pid-file = /data/mysql/3306/mysql.pid
 
# table_cache 参数设置表高速缓存的数目。每个连接进来，都会至少打开一个表缓存。#因此， table_cache 的大小应与 max_connections 的设置有关。例如，对于 200 个#并行运行的连接，应该让表的缓存至少有 200 × N ，这里 N 是应用可以执行的查询#的一个联接中表的最大数量。此外，还需要为临时表和文件保留一些额外的文件描述符。
#当 Mysql 访问一个表时，如果该表在缓存中已经被打开，则可以直接访问缓存；如果#还没有被缓存，但是在 Mysql 表缓冲区中还有空间，那么这个表就被打开并放入表缓#冲区；如果表缓存满了，则会按照一定的规则将当前未用的表释放，或者临时扩大表缓存来存放，使用表缓存的好处是可以更快速地访问表中的内容。执行 flush tables 会#清空缓存的内容。一般来说，可以通过查看数据库运行峰值时间的状态值 Open_tables #和 Opened_tables ，判断是否需要增加 table_cache 的值（其中 open_tables 是当#前打开的表的数量， Opened_tables 则是已经打开的表的数量）。即如果open_tables接近table_cache的时候，并且Opened_tables这个值在逐步增加，那就要考虑增加这个#值的大小了。还有就是Table_locks_waited比较高的时候，也需要增加table_cache。
open_files_limit = 10240
table_cache = 512
#非动态变量，需要重启服务
#指定MySQL可能的连接数量。当MySQL主线程在很短的时间内接收到非常多的连接请求，该参数生效，主线程花费很短的时间检查连接并且启动一个新线程。back_log参数的值指出在MySQL暂时停止响应新请求之前的短时间内多少个请求可以被存在堆栈中。如果系统在一个短时间内有很多连接，则需要增大该参数的值，该参数值指定到来的TCP/IP连接的侦听队列的大小。不同的操作系统在这个队列大小上有它自己的限制。试图设定back_log高于你的操作系统的限制将是无效的。默认值为50。对于Linux系统推荐设置为小于512的整数。
back_log = 600
#MySQL允许最大连接数
max_connections = 5000
#可以允许多少个错误连接
max_connect_errors = 6000
#使用–skip-external-locking MySQL选项以避免外部锁定。该选项默认开启
external-locking = FALSE
#设置最大包,限制server接受的数据包大小，避免超长SQL的执行有问题 默认值为16M，当MySQL客户端或mysqld服务器收到大于max_allowed_packet字节的信息包时，将发出“信息包过大”错误，并关闭连接。对于某些客户端，如果通信信息包过大，在执行查询期间，可能会遇到“丢失与MySQL服务器的连接”错误。默认值16M。
#dev-doc: http://dev.mysql.com/doc/refman/5.5/en/packet-too-large.html
max_allowed_packet = 32M
# Sort_Buffer_Size 是一个connection级参数，在每个connection（session）第一次需要使用这个buffer的时候，一次性分配设置的内存。
#Sort_Buffer_Size 并不是越大越好，由于是connection级的参数，过大的设置+高并发可能会耗尽系统内存资源。例如：500个连接将会消耗 500*sort_buffer_size(8M)=4G内存
#Sort_Buffer_Size 超过2KB的时候，就会使用mmap() 而不是 malloc() 来进行内存分配，导致效率降低。
#技术导读 http://blog.webshuo.com/2011/02/16/mysql-sort_buffer_size/
#dev-doc: http://dev.mysql.com/doc/refman/5.5/en/server-parameters.html
#explain select*from table where order limit；出现filesort
#属重点优化参数
sort_buffer_size = 8M
#用于表间关联缓存的大小
join_buffer_size = 1M
#服务器线程缓存这个值表示可以重新利用保存在缓存中线程的数量,当断开连接时如果缓存中还有空间,那么客户端的线程将被放到缓存中,如果线程重新被请求，那么请求将从缓存中读取,如果缓存中是空的或者是新的请求，那么这个线程将被重新创建,如果有很多新的线程，增加这个值可以改善系统性能.通过比较 Connections 和 Threads_created 状态的变量，可以看到这个变量的作用
thread_cache_size = 300
#设置thread_concurrency的值的正确与否, 对mysql的性能影响很大, 在多个cpu(或多核)的情况下，错误设置了thread_concurrency的值, 会导致mysql不能充分利用多cpu(或多核), 出现同一时刻只能一个cpu(或核)在工作的情况。thread_concurrency应设为CPU核数的2倍. 比如有一个双核的CPU, 那么thread_concurrency的应该为4; 2个双核的cpu, thread_concurrency的值应为8
#属重点优化参数
thread_concurrency = 8
#对于使用MySQL的用户，对于这个变量大家一定不会陌生。前几年的MyISAM引擎优化中，这个参数也是一个重要的优化参数。但随着发展，这个参数也爆露出来一些问题。机器的内存越来越大，人们也都习惯性的把以前有用的参数分配的值越来越大。这个参数加大后也引发了一系列问题。我们首先分析一下query_cache_size的工作原理：一个SELECT查询在DB中工作后，DB会把该语句缓存下来，当同样的一个SQL再次来到DB里调用时，DB在该表没发生变化的情况下把结果从缓存中返回给Client。这里有一个关建点，就是DB在利用Query_cache工作时，要求该语句涉及的表在这段时间内没有发生变更。那如果该表在发生变更时，Query_cache里的数据又怎么处理呢？首先要把Query_cache和该表相关的语句全部置为失效，然后在写入更新。那么如果Query_cache非常大，该表的查询结构又比较多，查询语句失效也慢，一个更新或是Insert就会很慢，这样看到的就是Update或是Insert怎么这么慢了。所以在数据库写入量或是更新量也比较大的系统，该参数不适合分配过大。而且在高并发，写入量大的系统，建系把该功能禁掉。
#重点优化参数（主库 增删改-MyISAM）
query_cache_size = 512M
#指定单个查询能够使用的缓冲区大小，缺省为1M
query_cache_limit = 2M
#默认是4KB，设置值大对大数据查询有好处，但如果你的查询都是小数据查询，就容易造成内存碎片和浪费
#查询缓存碎片率 = Qcache_free_blocks / Qcache_total_blocks * 100%
#如果查询缓存碎片率超过20%，可以用FLUSH QUERY CACHE整理缓存碎片，或者试试减小query_cache_min_res_unit，如果你的查询都是小数据量的话。
#查询缓存利用率 = (query_cache_size – Qcache_free_memory) / query_cache_size * 100%
#查询缓存利用率在25%以下的话说明query_cache_size设置的过大，可适当减小;查询缓存利用率在80%以上而且Qcache_lowmem_prunes > 50的话说明query_cache_size可能有点小，要不就是碎片太多。
#查询缓存命中率 = (Qcache_hits – Qcache_inserts) / Qcache_hits * 100%
query_cache_min_res_unit = 2k
default-storage-engine = MyISAM
#限定用于每个数据库线程的栈大小。默认设置足以满足大多数应用
thread_stack = 192K
# 设定默认的事务隔离级别.可用的级别如下:
# READ-UNCOMMITTED, READ-COMMITTED, REPEATABLE-READ, SERIALIZABLE
# 1.READ UNCOMMITTED-读未提交2.READ COMMITTE-读已提交3.REPEATABLE READ -可重复读4.SERIALIZABLE -串行
transaction_isolation = READ-COMMITTED
# tmp_table_size 的默认大小是 32M。如果一张临时表超出该大小，MySQL产生一个 The table tbl_name is full 形式的错误，如果你做很多高级 GROUP BY 查询，增加 tmp_table_size 值。
tmp_table_size = 246M
max_heap_table_size = 246M
#索引缓存大小: 它决定了数据库索引处理的速度，尤其是索引读的速度
key_buffer_size = 512M
# MySql读入缓冲区大小。对表进行顺序扫描的请求将分配一个读入缓冲区，MySql会为它分配一段内存缓冲区。read_buffer_size变量控制这一缓冲区的大小。如果对表的顺序扫描请求非常频繁，并且你认为频繁扫描进行得太慢，可以通过增加该变量值以及内存缓冲区大小提高其性能。
read_buffer_size = 4M
# MySql的随机读（查询操作）缓冲区大小。当按任意顺序读取行时(例如，按照排序顺序)，将分配一个随机读缓存区。进行排序查询时，MySql会首先扫描一遍该缓冲，以避免磁盘搜索，提高查询速度，如果需要排序大量数据，可适当调高该值。但MySql会为每个客户连接发放该缓冲空间，所以应尽量适当设置该值，以避免内存开销过大。
read_rnd_buffer_size = 16M
#批量插入数据缓存大小，可以有效提高插入效率，默认为8M
bulk_insert_buffer_size = 64M
# MyISAM表发生变化时重新排序所需的缓冲
myisam_sort_buffer_size = 128M
# MySQL重建索引时所允许的最大临时文件的大小 (当 REPAIR, ALTER TABLE 或者 LOAD DATA INFILE).
# 如果文件大小比此值更大,索引会通过键值缓冲创建(更慢)
myisam_max_sort_file_size = 10G
# 如果一个表拥有超过一个索引, MyISAM 可以通过并行排序使用超过一个线程去修复他们.
# 这对于拥有多个CPU以及大量内存情况的用户,是一个很好的选择.
myisam_repair_threads = 1
#自动检查和修复没有适当关闭的 MyISAM 表
myisam_recover
 
interactive_timeout = 120
wait_timeout = 120
 
innodb_data_home_dir = /data/mysql/3306/data
#表空间文件 重要数据
innodb_data_file_path = ibdata1:2000M;ibdata2:10M:autoextend
#这个参数用来设置 InnoDB 存储的数据目录信息和其它内部数据结构的内存池大小，类似于Oracle的library cache。这不是一个强制参数，可以被突破。
innodb_additional_mem_pool_size = 16M
#这对Innodb表来说非常重要。Innodb相比MyISAM表对缓冲更为敏感。MyISAM可以在默认的 key_buffer_size 设置下运行的可以，然而Innodb在默认的 innodb_buffer_pool_size 设置下却跟蜗牛似的。由于Innodb把数据和索引都缓存起来，无需留给操作系统太多的内存，因此如果只需要用Innodb的话则可以设置它高达 70-80% 的可用内存。一些应用于 key_buffer 的规则有 — 如果你的数据量不大，并且不会暴增，那么无需把 innodb_buffer_pool_size 设置的太大了
innodb_buffer_pool_size = 512M
#文件IO的线程数，一般为 4，但是在 Windows 下，可以设置得较大。
innodb_file_io_threads = 4
# 在InnoDb核心内的允许线程数量.
# 最优值依赖于应用程序,硬件以及操作系统的调度方式.
# 过高的值可能导致线程的互斥颠簸.
innodb_thread_concurrency = 8
#如果将此参数设置为1，将在每次提交事务后将日志写入磁盘。为提供性能，可以设置为0或2，但要承担在发生故障时丢失数据的风险。设置为0表示事务日志写入日志文件，而日志文件每秒刷新到磁盘一次。设置为2表示事务日志将在提交时写入日志，但日志文件每次刷新到磁盘一次。
innodb_flush_log_at_trx_commit = 2
#此参数确定些日志文件所用的内存大小，以M为单位。缓冲区更大能提高性能，但意外的故障将会丢失数据.MySQL开发人员建议设置为1－8M之间
innodb_log_buffer_size = 16M
#此参数确定数据日志文件的大小，以M为单位，更大的设置可以提高性能，但也会增加恢复故障数据库所需的时间
innodb_log_file_size = 128M
#为提高性能，MySQL可以以循环方式将日志文件写到多个文件。推荐设置为3M
innodb_log_files_in_group = 3
#推荐阅读 http://www.taobaodba.com/html/221_innodb_max_dirty_pages_pct_checkpoint.html
# Buffer_Pool中Dirty_Page所占的数量，直接影响InnoDB的关闭时间。参数innodb_max_dirty_pages_pct可以直接控制了Dirty_Page在Buffer_Pool中所占的比率，而且幸运的是innodb_max_dirty_pages_pct是可以动态改变的。所以，在关闭InnoDB之前先将innodb_max_dirty_pages_pct调小，强制数据块Flush一段时间，则能够大大缩短MySQL关闭的时间。
innodb_max_dirty_pages_pct = 90
# InnoDB 有其内置的死锁检测机制，能导致未完成的事务回滚。但是，如果结合InnoDB使用MyISAM的lock tables 语句或第三方事务引擎,则InnoDB无法识别死锁。为消除这种可能性，可以将innodb_lock_wait_timeout设置为一个整数值，指示 MySQL在允许其他事务修改那些最终受事务回滚的数据之前要等待多长时间(秒数)
innodb_lock_wait_timeout = 120
#独享表空间（关闭）
innodb_file_per_table = 0
 
#start mysqld with –slow-query-log-file=/data/mysql/3306/slow.log
slow_query_log
long_query_time = 1
 
replicate-ignore-db = mysql
replicate-ignore-db = test
replicate-ignore-db = information_schema
#配置从库上的更新操作是否写二进制文件，如果这台从库，还要做其他从库的主库，那么就需要打这个参数，以便从库的从库能够进行日志同步这个参数要和—logs-bin一起使用
log-slave-updates
log-bin = /data/mysql/3306/binlog/binlog
binlog_cache_size = 4M
#STATEMENT,ROW,MIXED
#基于SQL语句的复制(statement-based replication, SBR)，基于行的复制(row-based replication, RBR)，混合模式复制(mixed-based replication, MBR)。相应地，binlog的格式也有三种：STATEMENT，ROW，MIXED。
binlog_format = MIXED
max_binlog_cache_size = 64M
max_binlog_size = 1G
relay-log-index = /data/mysql/3306/relaylog/relaylog
relay-log-info-file = /data/mysql/3306/relaylog/relaylog
relay-log = /data/mysql/3306/relaylog/relaylog
expire_logs_days = 30
 
 
skip-name-resolve
#master-connect-retry = 10
slave-skip-errors = 1032,1062,126,1114,1146,1048,1396
server-id = 1
 
[mysqldump]
quick
max_allowed_packet = 32M
 
[myisamchk]
key_buffer_size = 256M
sort_buffer_size = 256M
read_buffer = 2M
write_buffer = 2M
 
[mysqlhotcopy]
interactive-timeout
```
补充：
http://ssydxa219.iteye.com/blog/2182739