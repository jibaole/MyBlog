---
title: 修改php时区的三种方法
tags:
  - php
categories:
  - Web development
description: 
toc: true
date: 2015-11-09 11:47:31
feature:
---

#### 修改PHP.ini
找到date.timezone这行，去掉前面的分号，改成：
```
date.timezone = Asia/Shanghai
```

#### 修改 .htaccess文件
修改.htaccess文件有两种办法，下面的两条语句只要有一条即可
```
php_value date.timezone Asia/Shanghai
```
OR
```
SetEnv TZ Asia/Shanghai
```

#### 修改PHP代码
也是下面的两条语句只要其中的一句即可
```
<? date_default_timezone_set('Asia/Shanghai'); ?>
```
OR
```
<? ini_set('date.timezone','Asia/Shanghai'); ?>
```

参考：https://www.ezloo.com/2009/08/php_timezone.html
