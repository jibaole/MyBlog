---
title: windows efi系统分区无法删除的解决办法
tags:
  - windows
categories:
  - Operating system
toc: false
date: 2014-11-17 11:17:38
keywords:
  - windows
  - efi
description:
  - windows
  - efi
feature:
---


是否以前安装过苹果系统留下的EFI系统分区？这个可以用Diskpart命令删除。

以Win7为例，首先，备份该磁盘所有分区的数据到其它硬盘！“这很重要！”

然后从开始菜单里找到附件－命令提示符，右键点击“命令提示符”，选择“以管理员模式运行”，在打开的CMD窗口里输入 `Diskpart` 回车，在 `Diskpart > 后面输入 list disk `回车，查看有EFI分区的是哪一个磁盘，假如为2，那么输入以下命令将焦点指定到该磁盘 `select disk 2` 回车，然后再输入`clean`回车，该磁盘所有信息即可清除。
