---
title: securecrt以命令行方式启动图形化界面
tags:
categories:
  - Linux server
toc: true
date: 2012-11-16 17:23:51
description:
feature:
---

>大家在使用客户端连接linux的时候，secureCRT是个非常棒的工具，但是由于不支持图形模式，在一些需要图形界面的时候就很麻烦，其实有个比较简单的解决办法，就是使用xmanager的passive.

### 准备
secureCRT和xmanager安装

### 步骤

#### 1.启动xmanager的passive

#### 2.在secureCRT中执行以下的命令
``` bash
export DISPLAY=192.168.25.1:0.0  //ip地址是本机的客户端地址

```
<!-- more -->
#### 3.测试
``` bash
xclock
```
如果在passive的窗口出现一个图形化的钟表，就ok,然后执行netca等图形化安装配置命令均可。
如果提示“Warning: Missing charsets in String to FontSet conversion”不需理会，只是字符转换问题。在执行netca的时候，提前设置
export LANG=en_US设置为英文环境即可。
如果有问题：
``` bash
$export  DISPLAY=X.X.X.X:0.0`
$source .bash_profile
$xclock
```