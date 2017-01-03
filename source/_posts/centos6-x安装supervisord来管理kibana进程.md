---
title: centos6.x安装supervisord来管理kibana进程
tags:
categories:
  - Linux server
toc: true
date: 2016-04-14 18:07:41
keywords:
  - centos
  - supervisord
description:
  - centos
  - supervisord
feature:
---

#### 安装supervisor
``` shell
yum install supervisor
```
<!-- more -->
#### 修改supervisor配置
``` shell
➜  hadoop-2.6.4 more /etc/supervisord.conf
[unix_http_server]
file=/tmp/supervisor.sock
 
[supervisord]
logfile=/var/log/supervisord.log
logfile_maxbytes=50MB
logfile_backups=10
loglevel=warn
pidfile=/var/log/supervisord.pid
nodaemon=false
minfds=1024
minprocs=200
user=root
childlogdir=/var/log/
[rpcinterface:supervisor]
supervisor.rpcinterface_factory = supervisor.rpcinterface:make_main_rpcinterface
[group:fluentd]
programs=kibana
[program:kibana]
command=/home/soft/kibana-4.5.1-linux-x64/bin/../node/bin/node /home/soft/kibana-4.5.1-linux-x64/bin/../src/cli
directory=.
autorestart=true
redirect_stderr=true
stdout_logfile=/var/log/kibana.log
stdout_logfile_maxbytes=50MB
stdout_logfile_backups=50
stdout_capture_maxbytes=1MB
stdout_events_enabled=false
loglevel=warn
```

#### 启动supervisor
``` shell
service supervisor start
```