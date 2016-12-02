---
title: linux指定某非root用户执行开机启动项的方法
tags:
categories:
  - Linux server
description: 参考：http://blog.sina.com.cn/s/blog_50d43ad501013pea.html
toc: false
date: 2016-08-09 15:11:50
feature:
---

root用户登录linux：
```
vim /etc/rc.d/rc.local
su sun -c "/home/sun/startrun.sh"
```
上面的语句表示在开机的时候使用sun用户执行/home/sun/startrun.sh

