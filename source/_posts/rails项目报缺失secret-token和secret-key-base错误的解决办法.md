---
title: rails项目报缺失secret_token和secret_key_base错误的解决办法
tags:
  - rails
categories:
  - Web development
description: 参考：http://stackoverflow.com/questions/32397607/missing-secret-token-and-secret-key-base-for-development-environment-set
toc: false
date: 2016-10-09 14:39:38
feature:
---

```
RAILS_ENV=development bundle exec rake generate_secret_token
```
OR
```
RAILS_ENV=production bundle exec rake generate_secret_token
```

