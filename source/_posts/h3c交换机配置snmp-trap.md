---
title: h3c交换机配置snmp trap
tags:
  - cacti
categories:
  - Monitoring technology
toc: true
date: 2012-05-14 16:55:50
description:
feature:
---

#### h3c交换机端配置snmp trap
```
[root@ops ~]# telnet 192.168.64.24 #以此服务器举例进行配置
Trying 192.168.64.24...
Connected to 192.168.64.24.
Escape character is '^]'.
******************************************************************************
* Copyright (c) 2004-2013 Hangzhou H3C Tech. Co., Ltd. All rights reserved.  *
* Without the owner's prior written consent,                                 *
* no decompiling or reverse-engineering shall be allowed.                    *
******************************************************************************
Login authentication
Password:
<H3C>super
Please input the password to change the privilege level, press CTRL_C to abort.
 Password:
User privilege level is 3, and only those commands can be used 
whose level is equal or less than this.
Privilege note: 0-VISIT, 1-MONITOR, 2-SYSTEM, 3-MANAGE
<H3C>system-view
System View: return to User View with Ctrl+Z.
[H3C]snmp-agent sys-info version v1 v2c #设置支持的snmp版本
[H3C]snmp-agent community read public #设置snmp的community值
[H3C]snmp-agent community write private
[H3C]snmp-agent trap enable #开启snmp trap
[H3C]snmp-agent target-host trap address udp-domain 192.168.80.41 params securityname public v1 #指定接收trap的目标服务器
注：192.168.80.41为Cacti Server
```
<!-- more -->

#### Cacti Server端验证SNMP配置是否正确
``` shell
[root@ops ~]# snmpwalk -c public -v 1 192.168.64.24 .1.3.6.1.2.1.11.30.0
SNMPv2-MIB::snmpEnableAuthenTraps.0 = INTEGER: enabled(1)
```
注：如果上面snmpwalk指令执行之后结果为“SNMPv2-MIB::snmpEnableAuthenTraps.0 = INTEGER: enabled(1)”则表示配置正确。