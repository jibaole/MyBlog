---
title: find和sed结合使用
tags:
  - shell
categories:
  - Scripting language
toc: true
date: 2011-11-14 16:10:54
keywords:
  - find
  - sed
description:
  - find
  - sed
feature:
---
``` shell
find . -name “*.php” -print0 | xargs -0 sed -i ‘s/192.168.1.206:/10.221.235.87:/g’
```

