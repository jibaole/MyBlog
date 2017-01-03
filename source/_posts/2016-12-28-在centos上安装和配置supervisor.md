---
title: 在centos上安装和配置supervisor
tags:
  - supervisor
categories:
  - Linux server
keywords:
  - centos
  - supervisor
description:
  - centos
  - supervisor
toc: true
date: 2016-12-28 15:47:32
thumbnail:
banner:
---

#### 安装supervisor
``` bash
yum install supervisor
```
如果使用yum无法安装，可以使用下面这种安装方法:
``` bash
sudo yum install python-setuptools
sudo easy_install pip
sudo pip install supervisor
```

#### 配置supervisor
安装好后，需要合建配置文件
```
echo_supervisord_conf > supervisord.conf
sudo cp supervisord.conf /etc/supervisord.conf
sudo mkdir /etc/supervisord.d/
sudo vi /etc/supervisord.conf
# 把下面的这两行的注释去掉，如果没有加上即可。
[include]
files = /etc/supervisord.d/*.conf
```
<!-- more -->
#### 创建一个配置文件
```
sudo touch /etc/supervisord.d/shadowsocks.conf
```
文件内容
```
[program: ] (  is name of program)
command=/bin/  //Execute command
process_name=%(program_name)s //if your numprocs is more than 1 it should be %(program_name)s_%(process_num)02d 
numprocs=1 //Number of your concurrent process Perfect for gearman
directory=/tmp //cd this directory before running
priority=999 //priority or program maximum is 999
autostart=true
autorestart=true
startretries=3 //number of retrying times if process is die
stopwaitsecs=10 //waiting xx second before before stop
user=chrism //exec by user should be “root”
//Log control 
stdout_logfile=/a/path
stdout_logfile_maxbytes=1MB

[program:shadowsocks] 
command=python /dir/server.py -c /dir/config.json
autorestart=true 
user=root
```


#### 设置开机启动
``` bash
sudo vi /etc/rc.d/init.d/supervisord
#!/bin/sh
#
# /etc/rc.d/init.d/supervisord
#
# Supervisor is a client/server system that
# allows its users to monitor and control a
# number of processes on UNIX-like operating
# systems.
#
# chkconfig: - 64 36
# description: Supervisor Server
# processname: supervisord

# Source init functions
. /etc/rc.d/init.d/functions

prog="supervisord"

prefix="/usr/"
exec_prefix="${prefix}"
prog_bin="${exec_prefix}/bin/supervisord"
PIDFILE="/var/run/$prog.pid"

start()
{
       echo -n $"Starting $prog: "
       daemon $prog_bin --pidfile $PIDFILE -c /etc/supervisord.conf
       [ -f $PIDFILE ] && success $"$prog startup" || failure $"$prog startup"
       echo
}

stop()
{
       echo -n $"Shutting down $prog: "
       [ -f $PIDFILE ] && killproc $prog || success $"$prog shutdown"
       echo
}

case "$1" in

 start)
   start
 ;;

 stop)
   stop
 ;;

 status)
       status $prog
 ;;

 restart)
   stop
   start
 ;;

 *)
   echo "Usage: $0 {start|stop|restart|status}"
 ;;

esac
```
再执行以下命令
``` bash
sudo chmod +x /etc/rc.d/init.d/supervisord
sudo chkconfig --add supervisord
sudo chkconfig supervisord on
sudo service supervisord start
```

参考：http://ju.outofmemory.cn/entry/201847