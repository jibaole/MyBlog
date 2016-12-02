---
title: 修改windows和ubuntu双系统启动顺序
tags:
categories:
  - Operating system
toc: true
date: 2012-11-14 10:30:56
description: 默认启动项是ubuntu，那么应该如何修改默认启动顺序呢？
feature:
---

* 找到`/etc/default/grub`文件，右键单击选择“以管理员权限打开”，（网上介绍的在终端中输入 `sudo /etc/default/grub`也是一样的效果，但我的方法更适合图形操作界面的习惯）。

* 打开grub文件后，可以看到其中第一行正文（以#开头的行是注释行）为：`GRUB_DEFAULT=0`，意思就是第0项为默认启动，大家也可以在启动时看到的启动菜单ubuntu在最顶上编号为0（计算机世界大多是从0开始排序的），将0改为windows前的编号即可，一般是4。改完记得存盘。

* 下面这一步也比较关键，在终端中输入`sudo update-grub`,也就是更新了grub.cfg文件，使刚才的改动生效。
重启机器，看看是不是修改成功了。