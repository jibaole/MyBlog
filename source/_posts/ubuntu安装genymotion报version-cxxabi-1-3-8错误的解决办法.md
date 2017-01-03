---
title: ubuntu安装genymotion报version cxxabi_1.3.8错误的解决办法
tags:
  - ubuntu
categories:
  - Operating system
toc: true
date: 2015-11-18 15:56:59
keywords:
  - ubuntu
  - genymotion
description:
  - ubuntu
  - genymotion
feature:
---

#### 报错信息
```
How to fix: [program name] /usr/lib/x86_64-linux-gnu/libstdc++.so.6: version CXXABI_1.3.8’ not found
```

#### 解决办法
``` bash
sudo add-apt-repository ppa:ubuntu-toolchain-r/test
sudo apt-get update
sudo apt-get install gcc-4.9g++-4.9
```