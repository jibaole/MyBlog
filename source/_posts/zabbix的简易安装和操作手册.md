---
title: zabbix的简易安装和操作手册
tags:
  - zabbix
categories:
  - Monitoring technology
toc: true
date: 2015-05-14 15:31:10
description:
feature:
---

### 一、Zabbix Server的安装

#### 1.安装环境信息
>Linux版本：Centos 6.5 x86_64
PHP版本：5.4.36
Apache版本：2.2.15
Mysql版本：5.1.73

#### 2.PHP模块安装（注：zabbix对PHP参数、PHP模块有特殊要求）
bcmath、mbstring、sockets、gd、libxml、libxml2、xmlwriter 、xmlreader、ctype、session、gettext （注：如未安装，可以通过yum –y install php-模块名进行安装）

<!-- more -->

#### 3.PHP配置参数
打开/et/php.ini配置文件，修改如下参数为如下值，否则无法安装zabbix安装。
```
max_execution_time = 300
memory_limit = 128M
post_max_size = 16M
upload_max_filesize = 2M
max_input_time = 300
date.timezone PRC
```

#### 4.下载安装zabbix
``` shell
cd /usr/local/src

wget “http://downloads.sourceforge.net/project/zabbix/ZABBIX%20Latest%20Stable/2.2.2/zabbix-2.2.2.tar.gz?r=http%3A%2F%2Fwww.zabbix.com%2Fdownload.php&ts=1395066528&use_mirror=jaist”

tar -xzvf zabbix-2.2.2.tar.gz

cd zabbix-2.2.2

./configure –prefix=/usr/local/zabbix-2.2.2/ –enable-server

–enable-agent –with-mysql –with-net-snmp –with-libcurl –with-libxml2

make install
```

#### 5.创建用户
``` shell
groupadd zabbix

useradd -g zabbix zabbix
```

#### 6.初始化数据库
zabbix server一共要导入3个sql文件。
``` shell
mysql -uroot –pxxx –h localhost

mysql> create database zabbix default charset utf8;
mysql> quit;

mysql -uroot -pxxx –h localhost zabbix < database/mysql/schema.sql

mysql -uroot -pxxx –h localhost zabbix < database/mysql/images.sql

mysql -uroot –pxxx –h localhost zabbix < database/mysql/data.sql
```

#### 7.配置zabbix
配置zabbix_server,如下：
``` shell
mkdir /etc/zabbix

cp /usr/local/src/zabbix-2.2.2/conf/zabbix_server.conf /etc/zabbix/

vim /etc/zabbix/zabbix_server.conf

DBName=zabbix
DBUser=root
DBPassword=xxx
DBPort=3306
```

#### 8.启动zabbix server
``` shell
/usr/local/zabbix-2.2.2/sbin/zabbix_server
```

### 二、Zabbix Client的安装Linux
#### 1.下载安装客户端
``` shell
cd /usr/local/src

wget “http://downloads.sourceforge.net/project/zabbix/ZABBIX%20Latest%20Stable/2.2.2/zabbix-2.2.2.tar.gz?r=http%3A%2F%2Fwww.zabbix.com%2Fdownload.php&ts=1395066528&use_mirror=jaist”

tar -xzvf zabbix-2.2.2.tar.gz

cd zabbix-2.2.2

./configure –prefix=/usr/local/zabbix-2.2.2/ –enable-agent

make install
```

#### 2.zabbix客户端配置
配置zabbix_agentd.conf文，如下：
``` shell
vim /usr/local/zabbix-2.2.2/etc/zabbix_agentd.conf

Server=zabbix server ip
ServerActive= zabbix server ip
Hostname=zabbix agent在web监控页面的名称
#其中Server和ServerActive都指定zabbixserver的IP地址，不同的是，前者是被动后者是主动。
```

#### 3.zabbix客户端启动
* Linux:
``` shell
/usr/local/zabbix-2.2.2/sbin/zabbix_agentd
```
  默认端口10050

* Windows server 2008 x64：
1. 将之前在linux中下载的zabbix agent包进行解压
2. 配置：找到conf下的配置文件 zabbix_agentd.win.conf ，修改LogFile、Server、Hostname这三个参数。具体配置如下：
  ```
  LogFile=c:zabbix_agentd.log
  Server=zabbix server ip
  Hostname=zabbix agent在web监控上面的名称
  ServerActive=zabbix server ip
  ```
  其中logfile是zabbix日志存放地址。
3. 安装agent
在windows管理员控制台下执行以下命令：
```
E:zabbix-2.2.2binwin64zabbix_agentd.exe -c E:zabbix-2.2.2confzabbix_agentd.win.conf –i
```
4. 启动agent客户端
启动命令如下：
```
E:zabbix-2.2.2binwin32zabbix_agentd.exe -c E:zabbix-2.2.2confzabbix_agentd.win.conf –s
```


### 三、Zabbix Web监控页面的配置
#### 1. zabbix管理网站配置
拷贝前端文件
``` shell
mkdir -p /var/www/html/zabbix

cp -rp /usr/local/src/zabbix-2.2.2/frontends/php/* /var/www/html/zabbix
```
#### 2. 在线配置zabbix
浏览器打开`http://zabbix server ip/zabbix`。如下是zabbix 2.2.2的安装界面，包括欢迎界面一共有6个步骤：
①欢迎界面
②php需求检查
③MySQL配置
④zabbix服务端详细信息
⑤zabbix安装前信息列表
⑥安装完成
如果失败了，一般情况是php对zabbix没有写权限
⑦zabbix安装完成。
注：zabbix默认登录用户名：Admin，密码：zabbix
3.添加主机
4.添加用户