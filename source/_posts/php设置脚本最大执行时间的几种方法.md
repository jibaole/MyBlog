---
title: php设置脚本最大执行时间的几种方法
tags:
  - php
categories:
  - Web development
toc: true
date: 2016-04-14 15:13:30
keywords:
  - php
  - 最大执行时间
description:
  - php
  - 最大执行时间
feature:
---


#### 一、在php.ini里面设置
```
max_execution_time = 45;
```

#### 二、通过PHP的ini_set 函数设置
```
ini_set("max_execution_time", "45");
```

#### 三、通过set_time_limit 函数设置
```
set_time_limit(45);
```
以上几个数字设置为0 则无限制，默认一般是30秒。偶尔有特殊情况，可以在脚本里面使用函数修改

