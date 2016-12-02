---
title: 在centos上安装postgresql9.5
tags:
  - postgresql
categories:
  - Database
toc: true
date: 2016-11-22 10:16:56
description: 
feature:
---

`PostgreSQL 9.5 Released`. PostgreSQL is an open source object-relational, highly scalable, SQL compliant database management system. PostgreSQL is developed at the University of California at Berkeley Computer Science Department. This article will help you for installing PostgreSQL on CentOS, RHEL and Fedora Systems.


#### Adding PostgreSQL Yum Repository

First step is to install PostgreSQL repository in your system, Use one of below commands as per your system architecture and operating system.
<!-- more -->
``` bash
CentOS/RHEL 7
# rpm -Uvh http://yum.postgresql.org/9.5/redhat/rhel-7-x86_64/pgdg-centos95-9.5-2.noarch.rpm

CentOS/RHEL 6
# rpm -Uvh http://yum.postgresql.org/9.5/redhat/rhel-6-x86_64/pgdg-redhat95-9.5-2.noarch.rpm

CentOS/RHEL 5
# rpm -Uvh http://yum.postgresql.org/9.5/redhat/rhel-5-x86_64/pgdg-redhat95-9.5-2.noarch.rpm

Fedora 23:
# rpm -Uvh http://yum.postgresql.org/9.5/fedora/fedora-23-x86_64/pgdg-fedora95-9.5-3.noarch.rpm

Fedora 22
# rpm -Uvh http://yum.postgresql.org/9.5/fedora/fedora-22-x86_64/pgdg-fedora95-9.5-3.noarch.rpm

Fedora 21
# rpm -Uvh http://yum.postgresql.org/9.5/fedora/fedora-21-x86_64/pgdg-fedora95-9.5-2.noarch.rpm
```
For more details visit [PostgreSQL repositories](http://yum.postgresql.org/repopackages.php) link page where you can get repository package rpm for various operating systems.

#### Installing PostgreSQL Server

After enabling PostgreSQL yum repository in your system use following command to install PostgreSQL 9.5 on your system with yum package manager.
``` bash
# yum install postgresql95-server postgresql95
```

#### Initializing PGDATA

After installing PostgreSQL server, It’s required to initialize it before using first time. To initialize database use below command.
``` bash
# /usr/pgsql-9.5/bin/postgresql95-setup initdb
```

Above command will take some time to initialize PostgreSQL first time. PGDATA environment variable contains the path of data directory.

PostgreSQL data directory Path: /var/lib/pgsql/9.5/data/

#### Start PostgreSQL Server

To start PostgreSQL service using following command as per your operating systems. Also enable PostgreSQL service to auto start on system boot.

For CentOS/RHEL 7 and Fedora 23
``` bash
# systemctl start postgresql-9.5
# systemctl enable postgresql-9.5
```
For CentOS/RHEL 6/5 and Fedora 22/21
``` bash
# service  postgresql-9.5 start
# chkconfig postgresql-9.5 on
```


#### Verify PostgreSQL Installation

After completing above steps, you have installed PostgreSQL 9.5 on your server, Let’s login to postfix to verify that installation completed successfully.
``` bash
# su - postgres
```
Use psql command to access PostgreSQL prompt with admin privileges.
``` bash
$ psql

psql (9.5.0)
Type "help" for help.
postgres=#
```


You may create password for user postgres for security purpose.
``` bash
postgres=# password postgres
```

Congratulation’s! You have successfully installed PostgreSQL Server. Read below article to install phpPgAdmin.

转自：http://tecadmin.net/install-postgresql-9-5-on-centos/#