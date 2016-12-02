---
title: android系统使用证书认证的wifi
tags:
  - others
categories:
  - Others
toc: false
date: 2016-11-29 14:02:14
description: 参考：http://bbs.gfan.com/android-85135-1-1.html
feature: 
---

![](http://i187.photobucket.com/albums/x62/dunefox_corsac/milestone/2010-04-06-15-45-27.png)
<!-- more -->
![](http://i187.photobucket.com/albums/x62/dunefox_corsac/milestone/2010-04-06-15-46-06.png)
`EAP方式`——PEAP（这个一般会自动选定）
`阶段2身份验证`——MSCHAPV2（这个需要自行选择 一般来说请参考同网络中其他平台智能手机的设置 比如塞班平台 或咨询你的网管 ）
`CA证书`——空着
`客户端证书`——空着
`身份`——网络提供单位给你用户名（一般是邮箱吧 例如abde09@XXXX.XX）
`匿名身份`——空着
`无线密码`——网络提供单位给你的密码
设定好后点连接