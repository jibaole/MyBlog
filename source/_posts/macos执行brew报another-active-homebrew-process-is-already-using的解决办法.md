---
title: macos执行brew报another active homebrew process is already using的解决办法
tags:
  - macos
categories:
  - Operating system
toc: false
date: 2014-11-18 16:13:23
keywords:
  - macos
  - brew
description:
  - macos
  - brew
feature:
---

``` bash
$ rm $(brew --cache)/Formula/openblas.brewing
```

or
``` bash
brew unlock
```

or

``` bash
ps aux | grep ruby
```

参考：https://github.com/Homebrew/legacy-homebrew/issues/17730