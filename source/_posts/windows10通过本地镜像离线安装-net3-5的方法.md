---
title: windows10通过本地镜像离线安装.net3.5的方法
tags:
  - windows
categories:
  - Operating system
toc: true
date: 2015-01-14 15:26:44
keywords:
  - windows10
  - .net3.5
description:
  - windows10
  - .net3.5
feature:
---
>很多程序都需要.Net 3.5的支持，而Windows 8/8.1、以及现在的Windows 10，默认都是没有启用.NET 3.5的。如果你运行某个程序，它需要这个组件的支持，那么它会提醒你下载，这个是在线下载的，实际上下载速度很慢。而再添加功能里，启用.net 3.5同样是在线下载，很慢。于是我们依然用之前操作Windows 8的方法，通过本地镜像来离线安装.NET 3.5。
<!-- more -->
方法/步骤：

1. 首先使用光驱（或者虚拟光驱挂载）windows10镜像，比如我挂载在H盘：
我们需要的是镜像目录下的`sources/sxs`，对应路径就是`H:sourcessxs`

2. 打开管理员模式的命令提示符，输入如下命令：
`dism.exe /online /enable-feature /featurename:NetFX3 /Source:H:sourcessxs`
上述命令中 `H:sourcessxs` 是自定义内容，就是你的sxs路径，注意自己修改。
输入后回车，大约1-2分钟即可安装完成。比在线下载要快N倍。

建议：安装完成之后重启电脑。
