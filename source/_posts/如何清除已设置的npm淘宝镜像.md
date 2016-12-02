---
title: 如何清除已设置的npm淘宝镜像
tags:
  - nodejs
categories:
  - Web development
toc: false
date: 2015-11-21 13:52:20
description: 
feature:
---

开发React Native时，通过下面命令将npm设置成淘宝镜像
``` bash
npm config set registry https://registry.npm.taobao.org
npm config set disturl https://npm.taobao.org/dist
```
现在需要将淘宝镜像去掉，还原成官方镜像，请问如何处理？

``` bash
npm config delete registry
npm config delete disturl
```
或者 
``` bash
npm config edit 
```
找到淘宝那两行,删除

转自：https://segmentfault.com/q/1010000004596263/a-1020000004596456