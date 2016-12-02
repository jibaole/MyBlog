---
title: kali ssh服务的开启
tags:
categories:
  - Linux server
toc: false
date: 2015-11-16 17:21:50
description:
feature:
---

照以下步骤进行配置和操作：
1. 修改sshd_config文件，命令为：vi /etc/ssh/sshd_config
2. 将#PasswordAuthentication no的注释去掉，并且将NO修改为YES //我的kali中默认是yes
3. 将#PermitRootLogin yes的注释去掉 //我的kali中默认去掉了注释
4. 启动SSH服务，命令为：/etc/init.d/ssh start // 或者service ssh start
5. 验证SSH服务状态，命令为：/etc/init.d/ssh status
