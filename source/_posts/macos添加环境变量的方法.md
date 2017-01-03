---
title: macos添加环境变量的方法
tags:
  - macos
categories:
  - Operating system
toc: false
date: 2014-06-17 16:37:27
keywords:
  - macos
  - 环境变量
description:
  - macos
  - 环境变量
feature:
---

方法：

将内容追加到 `~/.zshrc` 末尾

例如：

把`export HOMEBREW_BOTTLE_DOMAIN=https://mirrors.tuna.tsinghua.edu.cn/homebrew-bottles`添加到~/.zshrc,则使用homebrew来安装软件时，则会自动从清华大学source下载bottle文件(二进制预编译包).