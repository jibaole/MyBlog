---
title: 如何在mac中用命令行时用sublime打开文件
tags:
  - sublime
categories:
  - Development tools
toc: true
date: 2014-11-15 10:22:58
description: 
feature:
---

如果是在默认shell下
``` bash
sudo ln -s "/Applications/Sublime\ Text.app/Contents/SharedSupport/bin/subl" /usr/bin/subl
```
使用zsh的可以使用以下命令
``` bash
alias subl="'/Applications/Sublime Text.app/Contents/SharedSupport/bin/subl'"
alias nano="subl"
export EDITOR="subl"
```

转自：https://segmentfault.com/q/1010000002397241
