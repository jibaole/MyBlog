---
title: rails devise只允许管理员创建用户的两种思路
tags:
  - rails
categories:
  - Web development
toc: false
date: 2016-06-18 15:55:23
description:
feature:
---

* 使用devise wiki中的自定义注册账号路由来实现,具体实现方法
https://github.com/plataformatec/devise/wiki/How-To:-Customize-routes-to-user-registration-pages

* 给用户增加”是否是admin”的用户标记字段,具体实现方法
http://stackoverflow.com/questions/24875403/only-allow-admin-user-to-create-new-users-in-rails-with-devise-no-external-modu?rq=1