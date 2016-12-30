---
title: 使用shadowsocks加速github的使用
tags:
  - git
categories:
  - Version control
keywords:
  - github
  - shadowsocks
  - 加速
description:
  - shadowsocks加速github的使用
toc: false
date: 2016-12-28 15:42:38
thumbnail:
banner:
---

#### 解决办法
``` bash

git config --global http.proxy 'socks5://127.0.0.1:1080'
git config --global https.proxy 'socks5://127.0.0.1:1080'

```
>要在已经打开shadowsocks的情况下设置才有效