---
title: linux上源码编译安装mysql5.6
tags:
  - mysql
categories:
  - Database
toc: true
date: 2015-08-15 14:28:24
description: 
feature:
---

#### 1.下载MySQL
``` bash
wget http://mirrors.sohu.com/mysql/MySQL-5.6/mysql-5.6.30.tar.gz
```

#### 2.解压
``` bash
tar -zxvf mysql-5.6.30.tar.gz
chmod 0755 -R mysql-5.6.30
cd mysql-5.6.30
```

<!-- more -->
#### 3.编译
``` bash
cmake . -DCMAKE_INSTALL_PREFIX=/usr/local/mysql -DMYSQL_DATADIR=/usr/local/mysql3307 -DTMPDIR=/usr/local/mysql -DWITH_INNOBASE_STORAGE_ENGINE=1 -DWITH_ARCHIVE_STORAGE_ENGINE=1 -DWITH_BLACKHOLE_STORAGE_ENGINE=1 -DWITH_PERFSCHEMA_STORAGE_ENGINE=1 -DDEFAULT_CHARSET=utf8 -DDEFAULT_COLLATION=utf8_general_ci
make && make install
cd /usr/local/mysql
chown -R mysql:mysql .
```

#### 4.初始化MySQL
``` bash
/usr/local/mysql/scripts/mysql_install_db --user=mysql --datadir=/usr/local/mysql3307 --defaults-file=/usr/local/mysql/my.cnf

```

#### 5.启动MySQL
``` bash
/usr/local/mysql/bin/mysqld_safe --user=mysql --defaults-file=/usr/local/mysql/my.cnf &
  
```

参考：http://blog.csdn.net/wendi_0506/article/details/39478369
