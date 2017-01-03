---
title: 删除macos自带的openssl
tags:
  - macos
categories:
  - Operating system
description: 
  - macos
  - openssl
keywords:
  - macos
  - openssl
toc: true
date: 2014-11-09 17:00:55
feature:
---

#### 1.使用homebrew软链接新安装的openssl。
```
brew link openssl
```
如果提示创建软链接失败，则添加参数–force
```
brew link openssl --force
```
#### 2.新打开一个终端，重新执行openssl即为新安装版本。
```
openssl version
OpenSSL 1.0.2d 9 Jul 2015
/usr/bin/openssl version
OpenSSL 0.9.8zg 14 July 2015
```

参考：http://www.zhihu.com/question/36693783?sort=created