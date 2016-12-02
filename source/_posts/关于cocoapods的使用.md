---
title: 关于cocoapods的使用
tags:
  - ios
categories:
  - Mobile development
toc: true
date: 2016-10-21 11:20:45
description: 
feature:
---

关于CocoaPods，相信做iOS开发的应该都比较熟悉了。Cocoapods是一个用来管理第三方库的比较好用的管理工具。关于Cocoapods的东西不再多说，接下来进入正题。

我的软件版本如下:

Xcode版本为 7.3.1

CocoaPods版本为 1.0

传送门在此

[TOCM]
<!-- more -->

#### 使用终端进行管理

查询库的命令跟之前的一样
``` bash
$ pod search 要查询的库名
```
只不过查询完成后不能直接输入了，这时可以按
```
Q
```
键退出搜索页面。不过要注意，退出以后搜索结果就不见了，所以要找个地方保存你要用到的pod命令。 然后，关于podfile创建的部分。官网现在有一个智能创建的命令，用法和以前的差不太多。也是需要先cd切换到工程目录下，然后输入下面的命令:
``` bash
$ pod init
```
这时可以看到工程文件夹里面出现了熟悉的podfile文件。 然后打开里面的样式如下:

``` 
platform :ios, '8.0' use_frameworks!

target '工程名' do pod 'AFNetworking', '~> 2.6' pod 'ORStackView', '~> 3.0' pod 'SwiftyJSON', '~> 2.3' end 
```

这里偷点懒，用的是官方的例子

然后就可以开始安装了～命令没变:
``` bash
$ pod install
```
安装完成之后可以使用下面的命令打开工程:
``` bash
open 你工程名字.xcworkspace
```
注意

使用这个命令打开前必须要关闭.xcodeproj工程文件
然后就可以正常使用第三方库了。

#### 使用CocoaPods客户端

对，你没有看错，CocoaPods有自己的桌面客户端了。。。 下面是下载地址:

[官方地址](https://cocoapods.org/app)

[百度云地址](http://pan.baidu.com/s/1hrH0KT2)

提取码:j48p
打开以后的界面是这个样子的
![](http://img2.tuicool.com/JFFvueY.jpg!web)

然后
```
cmd+N
```
来选择工程

![](http://img0.tuicool.com/36nummi.jpg!web)
然后会跳转到这个页面
![](http://img0.tuicool.com/qABFjqu.jpg!web)

在这个页面里输入pod命令，然后点击
```
install
```
来安装

最后安装成功就是这个页面了 
![](http://img1.tuicool.com/v2eAjma.jpg!web)
就是这个样子 ^ _ ^

转自：http://www.tuicool.com/articles/uYFVzeq