---
title: rvm安装时报gpg命令找不到的解决办法
tags:
  - ruby
categories:
  - Scripting language
toc: false
date: 2016-05-18 16:03:11
description: 
feature:
---

Possible sources are:

* Package manager Homebrew: 
```
brew install gnupg gnupg2
```
or
```
$ brew install gpg2
```
or
```
$brew install gnupg
```
* Package manager MacPorts: 
```
sudo port install gnupg gnupg2
```
* Install from GPGTools, which also brings GUI applications and integration in Apple Mail

参考：http://stackoverflow.com/questions/27041885/how-to-resolve-gpg-command-not-found-error-during-rvm-installation
