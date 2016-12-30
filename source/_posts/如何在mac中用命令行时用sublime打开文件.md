---
title: 如何在mac中用命令行时用sublime打开文件
tags:
  - sublime
categories:
  - Development tools
toc: true
date: 2014-11-15 10:22:58
description: 
   - sublime text
   - macos
feature:
---

如果是在默认shell下
``` bash
ln -s "/Applications/Sublime Text.app/Contents/SharedSupport/bin/subl" ~/bin/subl
```
使用zsh的可以使用以下命令
``` bash
export EDITOR='subl -w'
```

转自：https://www.sublimetext.com/docs/3/osx_command_line.html
