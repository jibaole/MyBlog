---
title: 调整jenkins的显示语言
tags:
  - jenkins
categories:
  - Automated software
toc: true
date: 2015-11-11 11:27:02
description: 参考：http://it.zhaozhao.info/archives/50384
feature:
---

#### 1.进入到管理页面 –> 管理插件页面 –> 「可用的」页签
搜寻 `Locale`：Locale Plugin: This plugin controls the language of Jenkins
![](http://img.iteches.com/images/0C/9E977C8A034803A91B922827713B53.png)

#### 2.安装后, 可以回到管理Jenkins –> 设定系统页
就会发现有额外的设定 `locale` 选项出现, 在默认语言就可以填入`en_US, zh_CN`等语言。

`Ignore browser preference and force this language to all users`选项勾起来, 就会影响到所有的使用者。

按下页面最下面的储存按钮, 设定就会变更。

回到首页, 重新整理一下页面, 就可以看到默认语言改变了。


