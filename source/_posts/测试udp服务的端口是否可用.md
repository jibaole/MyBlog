---
title: 测试udp服务的端口是否可用
tags:
categories:
  - Linux server
toc: true
date: 2012-08-15 17:13:12
description: 
  - udp
  - 端口
keywords:
  - udp
  - 端口
feature:
---

#### 测试tcp服务的端口是否可用
``` bash
telnet ip port
```

#### 测试udp服务的端口是否可用
``` bash
nc -l -u 192.168.80.129 8001
```

转自：http://blog.chinaunix.net/uid-20788470-id-1841454.html