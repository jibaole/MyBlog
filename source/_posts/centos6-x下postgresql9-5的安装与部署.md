---
title: centos6.x下postgresql9.5的安装与部署
categories:
  - Database
toc: true
date: 2016-07-08 14:25:50
tags:
  - postgresql
description: 记录在centos6.x下postgresql9.5的安装与部署过程
feature:
---

#### 安装postgresql9.5
```
yum install https://download.postgresql.org/pub/repos/yum/9.5/redhat/rhel-6-x86_64/pgdg-centos95-9.5-2.noarch.rpm 
yum install postgresql95-server postgresql95-contrib
```


#### 初始化数据库
```
service postgresql-9.5 initdb 
```

#### 设置开机启动
```
chkconfig postgresql-9.5 on
```
<!-- more  -->
#### 重启数据库服务
数据库默认会创建一个postgres的数据库用户作为数据库的管理员，默认密码为空
```
service postgresql-9.5 restart 
```

#### 查询postgresql的版本
```
psql --version 
```

#### 修改数据库用户postgres的密码 
```
# su - postgres 
$ psql 
# ALTER USER postgres WITH PASSWORD '123456'; 
# select * from pg_shadow;
create database stockhq owner postgres;#创建数据库  
```

#### 查看端口占用
```
netstat -anp |grep 5432 
```

#### 开启远程访问
`vim /var/lib/pgsql/9.5/data/postgresql.conf`
修改 
```
listen_addresses ='*'

/var/lib/pgsql/9.4/data/pg_hba.conf 
```
修改 
```
host all all  0.0.0.0/0 md5
```


#### 补充
1.在centos7中安装postgresql9.5.2
```
yum install https://download.postgresql.org/pub/repos/yum/9.5/redhat/rhel-7-x86_64/pgdg-centos95-9.5-2.noarch.rpm 
yum install postgresql95-server postgresql95-contrib 

```

2.初始化库
```
/usr/pgsql-9.5/bin/postgresql95-setup initdb 
```

3.启动数据库
```
systemctl enable postgresql-9.5.service 
systemctl start postgresql-9.5.service 
systemctl restart postgresql-9.5.service
```

4.进入数据库
数据目录自动安装在/var/lib/pgsql/9.5/data
```
su postgres 
psql 
\l #查看所有库 
\q #退出 
```

5.防火墙的设置
```
#停止firewall 
systemctl stop firewalld.service 
#禁止firewall开机启动 
systemctl disable firewalld.service 
#重启防火墙使配置生效 
systemctl restart iptables.service 
#设置防火墙开机启动 
systemctl enable iptables.service
```