---
title: linux下安装jdk、jboss、db2
tags:
categories:
  - Linux server
toc: true
date: 2014-07-17 15:42:13
keywords:
  - linux
  - jdk
  - jboss
  - db2
description:
  - linux
  - jdk
  - jboss
  - db2
feature:
---

>First at all, on CentOS 4.5, I have SELinux disabled and firewall/iptables enabled.

#### Install JDK 1.5 on CentOS 4.5
In order to use EJB from Jboss, you will need to install JDK 1.5.

1. CentOS 4.5 comes with Yum (the package management), which makes it much easier to install packages, but there are not so much packages managed in Yum’s default repositories, like Java related packages, so you will need to install an repository for Yum called “jpackage” first.
  ``` bash
  cd /etc/yum.repos.d
  wget http://www.jpackage.org/jpackage17.repo
  ```
<!-- more -->
2. Update your yum repositories
  ``` bash
  yum check-update
  ```
3. install JDK 1.5
  ``` bash
  cd /usr/local/src
  wget ftp://jpackage.hmdc.harvard.edu/JPackage/1.7/generic/non-free/SRPMS/java-1.5.0-sun-1.5.0.13-1jpp.nosrc.rpm
  download jdk-1_5_0_13-linux-i586.bin from sun
  (under Linux Platform - Java Development Kit 5.0 Update 13 / Linux self-extracting file) and copy to /usr/src/redhat/SOURCES/
  rpmbuild –rebuild /usr/local/src/java-1.5.0-sun-1.5.0.13-1jpp.nosrc.rpm
  rpm -ivh /usr/src/redhat/RPMS/i586/java-1.5.0-sun-1.5.0-13-1jpp.i586.rpm
  rpm -ivh /usr/src/redhat/RPMS/i586/java-1.5.0-sun-devel-1.5.0.13-1jpp.i586.rpm
  ```
4. set up environment variable
create a file /etc/profile.d/jdk.sh which contains this line in it:
  ``` bash
  export JAVA_HOME=/usr/lib/jvm/java-1.5.0-sun-1.5.0.13
  and make it executable:
  chmod +x /etc/profile.d/jdk.sh
  ```

#### Install Jboss 4.2.2 GA on CentOS 4.5
1. download jboss 4.2.2 GA (or the latest version)
  download jboss from:
  http://sourceforge.net/project/showfiles.php?group_id=22866&package_id=16942
  and extract to /usr/local/, rename the directory from Jboss-4.x.x.GA
  to jboss
  now will have jboss files under /usr/local/jboss directory

2. create jboss user and group
  ``` bash
  groupadd jboss
  useradd -d /usr/local/jboss -g jboss jboss
  ```
3. change password for user jboss
  ``` bash
  passwd jboss
  ```
4. change ownership of the whole directory /usr/local/jboss to
  ```
  jboss:jboss
  chown jboss:jboss -R /usr/local/jboss
  ```

5. make the jboss control script /etc/init.d/jboss
  ```
  cp /usr/local/jboss/bin/jboss_init_redhat.sh /etc/init.d/jboss
  ```

make necessary changes in the file, for example, instead of binding the ip to 127.0.0.1 only, you might want to bind it to external ip, then put this line in the file:
```
JBOSS_HOST=0.0.0.0
make the file executable
chmod +x /etc/init.d/jboss
```
1. enable auto start with OS by making symbolic link of /etc/init.d/jboss to system start up level directory
find out the default start level you are using:
open the file /etc/inittab, look at the beginning of the file, you will see something like this:
  ```
  id:5:initdefault:
  ```
  which means your default run level is 5
  ``` bash
  cd /etc/rc5.d (if your default run level is 3, you will need to change directory to /etc/rc3.d)
  ln -s ../init.d/jboss S96jboss
  ```

2. how to start/stop jboss manually ?

  to start:
  ``` bash
  /etc/init.d/jboss start
  ```
  or
  ``` bash
  service jboss start
  ```
  to stop:
  ``` bash
  /etc/init.d/jboss stop
  ```
  or
  ``` bash
  service jboss stop
  ```
3. add exception to firewall
  add this line to 
  ```
  /etc/sysconfig/iptables -A RH-Firewall-1-INPUT -m state –state NEW -m tcp -p tcp –dport 8080 -j ACCEPT
  ```
  restart your firewall:
  ``` bash
  /etc/init.d/iptables restart
  ```
4. start jboss and type in http://127.0.0.1:8080 in your browser to check if jboss is running

#### Install DB2 Express-C 9.5 on CentOS 4.5
1. fix missing /usr/lib/libstdc++.so.5 (db2 was compiled with older c++ library)
  ``` bash
  yum install compat-libstdc++-33
  ```
2. install required package libaio
  ``` bash
  yum install libaio
  ```
3. install db2
  download DB2 Express-C for Linux zip file from:
  http://www-306.ibm.com/software/data/db2/express/download.html
  unzip
  run db2_install and follow the steps

4. add exceptions on firewall
add these three lines into /etc/sysconfig/iptables:
  ```
  -A RH-Firewall-1-INPUT -m state –state NEW -p udp -m udp –dport 523 -j ACCEPT
  -A RH-Firewall-1-INPUT -m state –state NEW -p tcp -m tcp –dport 523 -j ACCEPT
  -A RH-Firewall-1-INPUT -m state –state NEW -p tcp -m tcp –dport 50000 -j ACCEPT
  ```
5. restart firewall:
  ``` bash
  /etc/init.d/iptables restart
  ```
6. create db2 users
  ``` bash
  groupadd db2grp1
  groupadd db2fgrp1
  groupadd dasadm1
  useradd -g db2grp1 -m -d /opt/ibm/db2/V9.5/db2inst1 db2inst1 -p PASSWORD
  useradd -g db2fgrp1 -m -d /opt/ibm/db2/V9.5/db2fenc1 db2fenc1 -p PASSWORD
  useradd -g dasadm1 -m -d /opt/ibm/db2/V9.5/dasusr1 dasusr1 -p PASSWORD
  ```
7. create a db2 instance (I chose the
default install path)
  ``` bash
  cd /opt/ibm/db2/V9.5/instance
  ./db2icrt -p 50000 -u db2fenc1 db2inst1
  ```
8. create DAS(Administration Server)
  ``` bash
  cd /opt/ibm/db2/V9.5/instance
  ./dascrt -u dasadm1
  ```
9. enable db2 auto start with OS
  ``` bash
  su - db2inst1
  db2iauto -on db2inst1
  ```
