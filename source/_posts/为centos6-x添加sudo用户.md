---
title: 为centos6.x添加sudo用户
tags:
  - centos
  - sudo
categories:
  - Linux server
description: 
  - centos sudo
toc: true
date: 2011-11-08 16:40:55
feature:
---

#### 添加用户
```
adduser mynewuser
passwd mynewuser
```

#### 为新增用户添加sudo权限
```
visudo
```

<!-- more -->
找到
```
## Allow root to run any commands anywhere
root ALL=(ALL) ALL
```
在上面代码下面天下如下内容，按:wq退出即可
```
mynewuser ALL=(ALL) ALL
```

参考：https://www.liquidweb.com/kb/how-to-add-a-user-and-grant-root-privileges-on-centos-6-5/

