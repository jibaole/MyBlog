---
title: git ignore file
tags:
  - git
categories:
  - Version control
toc: true
date: 2015-11-21 10:13:15
keywords:
  - git
  - ignore
description:
  - git
  - ignore
feature:
---

### Create a local .gitignore
``` bash
touch .gitignore
```
如果文件已经commit但还是想ignore，可以通过下面的命令进行排除：
``` bash
git rm --cached FILENAME
```

#### Create a global .gitignore
``` bash
git config --global core.excludesfile ~/.gitignore_global
```

转自：https://help.github.com/articles/ignoring-files/