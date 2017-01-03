---
title: activeadmin activerecord model字段国际化实现
tags:
  - rails
categories:
  - Web development
toc: false
date: 2016-07-18 11:05:08
keywords:
  - rails
  - activeadmin
  - model
  - i18n
  - 国际化
description:
  - rails
  - activeadmin
  - model
  - i18n
  - 国际化 
feature:
---

``` yaml
activerecord:
  attributes:
    user:
      email: ${dictionary.email}
      name: ${dictionary.name}
      password: ${dictionary.password}
      password_confirmation: ${dictionary.confirmation}
  models:
    user: User
```

参考：http://stackoverflow.com/questions/11097572/refactoring-ruby-on-rails-i18n-yaml-files-using-dictionaries