---
title: mysql5.1升级到mysql5.6官方指导方法
tags:
  - mysql
categories:
  - Database
toc: false
date: 2015-11-14 17:14:47
description:
feature:
---
A MySQL server package (mysql-server-5.1.73-5.el6_7.1.x86_64) is installed.

The current MySQL server package is provided by a different
vendor (CentOS) than MySQL AB, Sun Microsystems, Inc., or Oracle and/or its affiliates.
Some files may be installed to different locations, including log
files and the service startup script in /etc/init.d/.

Upgrading directly from MySQL 5.1 to MySQL 5.6 may not
be safe in all cases. A manual dump and restore using mysqldump is
recommended. It is important to review the MySQL manual’s Upgrading
section for version-specific incompatibilities.

A manual upgrade is required.

* Ensure that you have a complete, working backup of your data and my.cnf
files
* Shut down the MySQL server cleanly
* Remove the existing MySQL packages. Usually this command will
list the packages you should remove:
rpm -qa | grep -i ‘^mysql-‘

You may choose to use ‘rpm –nodeps -ev ‘ to remove
the package which contains the mysqlclient shared library. The
library will be reinstalled by the MySQL-shared-compat package.

* Install the new MySQL packages supplied by Oracle and/or its affiliates
* Ensure that the MySQL server is started
* Run the ‘mysql_upgrade’ program
This is a brief description of the upgrade process. Important details
can be found in the MySQL manual, in the Upgrading section.
