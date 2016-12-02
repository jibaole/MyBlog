---
title: devstack instance console keyboard error报错的解决办法
tags:
  - openstack
categories:
  - Virtualization technology
toc: false
date: 2016-11-22 16:01:16
description: 
feature:
---

I had the same problem, it seems to be a bug in noVNC, I also saw it at: https://bugs.launchpad.net/horizon/+bug/1622684 (https://bugs.launchpad.net/horizon/+b...)

I was able to work around it by using an older version of noVNC. go to `/opt/stack/noVNC` and do a `git checkout v0.6.0`. Then reload the the console for the instance.

Alternatively you can add `NOVNC_BRANCH=v0.6.0` to your `local.conf` file, and restack.

参考：https://ask.openstack.org/en/question/96772/devstack-instance-console-keyboard-error/