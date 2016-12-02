---
title: 使用find xargs时报xargs unterminated quote的解决办法
tags:
  - shell
categories:
  - Scripting language
toc: true
date: 2014-07-18 16:34:04
description: 
feature:
---

#### 问题
I want to get the number of lines in my application. I am using this code:
``` bash
find . "(" -name "*.m" -or -name "*.h" ")" -print | xargs wc -l
```
It is working fine in other applications but for one of my applications it is giving the error "xargs unterminated quote".

#### 解决办法
I tried to remove all .svn folder and below is worked for me: 
``` bash
find ./ -name ".svn" -print0 | xargs -0 rm -Rf –
```

参考：http://stackoverflow.com/questions/11649872/getting-error-xargs-unterminated-quote-when-tried-to-print-the-number-of-lines