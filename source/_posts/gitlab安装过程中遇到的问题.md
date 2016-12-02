---
title: gitlab安装过程中遇到的问题
tags:
  - git
categories:
  - Version control
toc: true
date: 2016-08-11 11:23:01
description:
feature:
---

gitlab安装参考官方教程。安装过程遇到的问题总结如下：

* 页面中会报gravatar头像被墙访问超时的问题，参考：http://my.oschina.net/anylain/blog/355797?fromerr=wrXUCL4g

* 启动后访问首页遇到502报错，参考：http://stackoverflow.com/questions/33254100/502-whoops-gitlab-is-taking-too-much-time-to-respond

* rpm安装gitlab文件默认安装在两个目录:/opt/gitlab和/var/opt/gitlab，安装好之后测试迁移目录到大分区然后添加软链接的方式，gitlab-ctl tail会发现有很多报错，并且git无法正常提交，估计是因为软链接权限问题，具体没做过多尝试，如果后续会考虑用rpm安装方式，建议对/var目录增加分区。