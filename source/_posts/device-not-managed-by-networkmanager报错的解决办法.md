---
title: device not managed by networkmanager报错的解决办法
tags:
  - others
categories:
  - Linux server
toc: true
date: 2014-03-17 15:57:20
keywords:
  - networkmanager
description:
  - networkmanager
feature:
---

#### Remove Network Manager from startup Services
``` bash
chkconfig NetworkManager off
```
#### Add Default Net Manager
``` bash
chkconfig network on
```


#### Stop NetworkManager first
``` bash
service NetworkManager stop
```

#### and then start Default Manager
``` bash
service network start
```