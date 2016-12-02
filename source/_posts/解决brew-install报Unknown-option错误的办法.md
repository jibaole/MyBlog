---
title: 解决brew install报Unknown option错误的办法
categories:
  - Operating system
toc: false
date: 2016-05-08 15:25:55
tags:
  - macos
description: 参考：http://stackoverflow.com/questions/21498991/head-usage-unknown-option-1-n-error-possibly-ruby-related
feature:
---

解决该问题的两个办法：
* 从环境变量$PATH中删除`/Applications/XAMPP/xamppfiles/bin`
* 在`/Applications/XAMPP/xamppfiles/bin`中执行`mv HEAD HTTP_HEAD`
