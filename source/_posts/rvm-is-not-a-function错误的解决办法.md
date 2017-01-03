---
title: rvm is not a function错误的解决办法
tags:
  - rails
categories:
  - Web development
toc: false
date: 2016-03-15 17:01:57
keywords:
  - rails
  - rvm
description:
  - rails
  - rvm
feature:
---

#### 解决办法
``` bash
echo '[[ -s "$HOME/.rvm/scripts/rvm" ]] && source "$HOME/.rvm/scripts/rvm"'>>~/.zshrc
```

