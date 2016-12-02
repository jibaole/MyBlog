---
title: tomcat的三种高级connector运行模式
tags:
  - tomcat
categories:
  - Web server
toc: true
date: 2013-11-16 16:22:30
description:
feature:
---

#### 1.bio
默认的模式,性能非常低下,没有经过任何优化处理和支持.

#### 2.nio
利用java的异步io护理技术,no blocking IO技术.
想运行在该模式下，直接修改server.xml里的Connector节点,修改protocol为

启动后,就可以生效。
<!-- more -->
#### 3.apr
安装起来最困难,但是从操作系统级别来解决异步的IO问题,大幅度的提高性能.
必须要安装apr和native，直接启动就支持apr。下面的修改纯属多余，仅供大家扩充知识,但仍然需要安装apr和native
如nio修改模式,修改protocol为org.apache.coyote.http11.Http11AprProtocol