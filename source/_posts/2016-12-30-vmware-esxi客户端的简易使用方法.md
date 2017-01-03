---
title: vmware esxi客户端的简易使用方法
tags:
  - vmware
categories:
  - Virtualization technology
keywords:
  - vmware esxi
  - 客户端
description:
  - vmware esxi
  - 客户端
toc: true
date: 2016-12-30 16:55:04
thumbnail:
banner:
---

#### VMware-viclient下载
http://vsphereclient.vmware.com/vsphereclient/1/6/1/8/0/7/1/VMware-viclient-all-5.5.0-1618071.exe

#### 准备工作
安装前确认是否安装了.net framwork 3.5，因为该客户端依然此软件，所以务必安装。
如果是windows8，8.1或者windows10,可以参考下面的内容进行安装：
很多程序都需要.Net 3.5的支持，而Windows 8/8.1、以及现在的Windows 10，默认都是没有启用.NET 3.5的。如果你运行某个程序，它需要这个组件的支持，那么它会提醒你下载，这个是在线下载的，实际上下载速度很慢。而再添加功能里，启用.net 3.5同样是在线下载，很慢。于是我们依然用之前操作Windows 8的方法，通过本地镜像来离线安装.NET 3.5。
方法/步骤：
1. 首先使用光驱（或者虚拟光驱挂载）windows10镜像，比如我挂载在H盘：
我们需要的是镜像目录下的sources/sxs，对应路径就是H:\sources\sxs
2. 打开管理员模式的命令提示符，输入如下命令：
```
dism.exe /online /enable-feature /featurename:NetFX3 /Source:H:\sources\sxs

```
<!-- more -->
上述命令中  H:\sources\sxs  是自定义内容，就是你的sxs路径，注意自己修改。
输入后回车，大约1-2分钟即可安装完成。比在线下载要快N倍。
建议：安装完成之后重启电脑。

#### 使用方法
双击桌面的vsphereclient，输入server ip以及管理账号和密码即可登录访问，这里就不截图了