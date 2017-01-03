---
title: 修改ruby gem的默认source为taobao source
tags:
  - rails
categories:
  - Web development
toc: true
date: 2016-05-09 17:18:57
description: 
  - ruby
  - gem
  - source
  - taobao
keywords:
  - ruby
  - gem
  - source
  - taobao
feature:
---

#### 1.首先确认一下当前source镜像地址
```
gem sources -l
```
默认为https://rubygems.org/
```
*** CURRENT SOURCES ***
https://rubygems.org/
```
#### 2.删除gem的默认source替换为淘宝的source
```
gem sources -r https://rubygems.org/ -a https://ruby.taobao.org
```
再执行gem sources -l检查一下
```
*** CURRENT SOURCES ***
https://ruby.taobao.org
```
#### 3.最后提供几个其他可用的source

– 中山大学：http://mirror.sysu.edu.cn/rubygems/
– 山东理工大学：http://ruby.sdutlinux.org/

参考：http://www.tuicool.com/articles/3YFfYfy

