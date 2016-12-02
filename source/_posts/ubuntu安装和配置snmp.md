---
title: ubuntu安装和配置snmp
tags:
  - snmp
categories:
  - Linux server
toc: true
date: 2013-11-18 10:42:14
description: 转自：http://langzixin.iteye.com/blog/810610
feature:
---

>SNMP(Simple Network Management Protocol,简单网络管理协议) 的前身是简单网关监控协议(SGMP)，用来对通信线路进行管理。随后，人们对SGMP进行了很大的修改，特别是加入了符合Internet定义的SMI 和MIB：体系结构，改进后的协议就是著名的SNMP。SNMP的目标是管理互联网Internet上众多厂家生产的软硬件平台，因此SNMP受 Internet标准网络管理框架的影响也很大。现在SNMP已经出到第三个版本的协议，其功能较以前已经大大地加强和改进了。
 
#### 1、首先安装snmp
``` bash
   sudo apt-get install snmpd
```
使用命令 
``` bash
lsof -i:161
COMMAND   PID USER   FD   TYPE     DEVICE SIZE/OFF NODE NAME  
snmpd   32460 snmp    6u  IPv4 1300464995      0t0  UDP localhost:snmp  
```
则服务已经启动，否则使用service snmpd start启动服务
<!-- more -->
#### 2、本地测试（获取系统有关信息）
  
``` bash
snmpwalk -v 1 -c public localhost  .1.3.6.1.2.1.2.2.1.2  
```
如果提示snmpwalk：无此命令
``` bash
sudo apt-get install snmp
```
安装完毕后，再次执行上面命令，无法获得系统相关信息
 
#### 3、更改配置文件
```  bash
sudo gedit /etc/snmp/snmpd.conf
```
``` 
com2sec  paranoid  default         public  
  
＃com2sec readonly  default         public  
```
更改为
```
＃com2sec  paranoid  default         public  
  
com2sec readonly  default         public  
```
service snmpd restart 重启服务，这样通信community public就有了对所有项的读权限
 
#### 4、可是如果其它主机访问该ubuntu下的snmp服务，无法获取信息
 
原因：终端执行命令   
```
lsof -i:161 
COMMAND   PID USER   FD   TYPE     DEVICE SIZE/OFF NODE NAME  
snmpd   32460 snmp    6u  IPv4 1300464995      0t0  UDP localhost:snmp  
```
发现端口监听在 localhost 上 , 这个就是一个明显的问题所在
 
修改配置文件   
``` bash
sudo gedit /etc/default/snmpd
```
   将
```
SNMPDOPTS='-Lsd -Lf /dev/null -u snmp -I -smux -p /var/run/snmpd.pid 127.0.0.1'  
```
  修改为
```
SNMPDOPTS='-Lsd -Lf /dev/null -u snmp -I -smux -p /var/run/snmpd.pid   
```

``` bash
service snmpd restart 
```
重启服务，现在从别的机器再访问Ubuntu server 的snmp已经OK了