---
title: php用字符串和变量组成变量
tags:
  - php
categories:
  - Web development
toc: false
date: 2015-07-17 11:10:05
description: 转自：http://hi.baidu.com/oyvfhp/item/2bee2d200aba2b152a0f1cfc
feature:
---

理论很简单,将字符串和变量组合在一起形成对另一个变量的操作.同样也可以是数组
``` php
$FFabcd = '组合变量';
$a = 'abcd';
$ay = array('FF','abcd');
echo ${'FF'.$a};
echo ${$ay[0].$ay[1]}; //同样,数组可以和字符串和变量组合,其中最关键就是使用{}包起来
```
