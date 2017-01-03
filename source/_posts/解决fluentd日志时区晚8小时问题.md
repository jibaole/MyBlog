---
title: 解决fluentd日志时区晚8小时问题
tags:
  - fluentd
categories:
  - Big Data technology
toc: false
date: 2016-04-14 17:16:10
description: 
  - fluentd
  - 时区
  - timezone
keywords:
  - fluentd
  - 时区
  - timezone
feature:
---

```
type webhdfs
host 192.168.80.41
port 50070
path /log/fluentd/%Y%m%d/fluentd.log.${hostname}
time_slice_format %Y%m%d
    time_slice_wait 10m
time_format %Y-%m-%d %H:%M:%S
localtime
flush_interval 10s
```
