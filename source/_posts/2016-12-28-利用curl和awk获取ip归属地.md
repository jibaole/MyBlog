---
title: 利用curl和awk获取ip归属地
tags:
  - curl
categories:
  - Linux server
keywords:
  - curl
  - awk
  - ip归属地
description:
  - curl
  - awk
toc: false
date: 2016-12-28 16:59:27
thumbnail:
banner:
---

Example:
```
curl -s "http://xxx" |awk -F '"' '{print $4}'
```