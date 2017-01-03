---
title: rails的常用日期取值
tags:
  - rails
categories:
  - Web development
toc: true
date: 2016-06-21 11:02:26
keywords:
  - rails
  - 日期
description:
  - rails
  - 日期
feature: 
---

#### 获取当前时间

当前时间 
``` ruby
Time.now
```
UTC时间，国际标准时间 
``` ruby
Time.now.utc
```
#### 日相关

今日开始时间00:00:00 
``` ruby
Time.now.beginning_of_day

=> 2016-07-24 00:00:00 +0800
```
<!-- more -->
今日结束23:59:59 
``` ruby
Time.now.at_end_of_day

=> 2016-07-24 23:59:59 +0800
```
昨日开始时间00:00:00 
``` ruby
(Time.now - 1.day).beginning_of_day

=> 2016-07-23 00:00:00 +0800
```
昨日结束23:59:59 
``` ruby
(Time.now - 1.day).at_end_of_day

=> 2016-07-23 23:59:59 +0800
```
#### 周相关

当前日期的周一 
``` ruby
Time.now.at_beginning_of_week

=> 2016-07-18 00:00:00 +0800
```
当前日期的周日 
``` ruby
Time.now.at_end_of_week

=> 2016-07-24 23:59:59 +0800
```
上周一 
``` ruby
(Time.now - 1.week).at_beginning_of_week

=> 2016-07-11 00:00:00 +0800
```
上周日 
``` ruby
(Time.now - 1.week).at_end_of_week

=> 2016-07-17 23:59:59 +0800
```
#### 月相关

当前日期所在月的月初 
``` ruby
Time.now.beginning_of_month

=> 2016-07-01 00:00:00 +0800
```
当前日期所在月的月末 
``` ruby
Time.now.end_of_month

=> 2016-07-31 23:59:59 +0800
```
上个月的月初 
``` ruby
(Time.now - 1.month).beginning_of_month

=> 2016-06-01 00:00:00 +0800
```
上个月的月末 
``` ruby
(Time.now - 1.month).end_of_month

=> 2016-06-30 23:59:59 +0800
```
#### 年相关

当前年初 
``` ruby
Time.now.beginning_of_year

=> 2016-01-01 00:00:00 +0800
```
当前年末 
``` ruby
Time.now.end_of_year

=> 2016-12-31 23:59:59 +0800
```
#### 日期转化方法

转化成年月日时分秒格式： 
``` ruby
Time.now.strftime('%Y-%m-%d %H:%M:%S')

=> "2016-07-24 15:07:13"
```
转化成年月日格式： 
``` ruby
Time.now.strftime('%Y-%m-%d')

=> "2016-07-24"
```
转化成汉字形式的年月日 
``` ruby
Time.now.strftime('%Y年-%m月-%d日 %H时:%M分:%S秒')

=> "2016年-07月-24日 15时:17分:53秒"
```

转自：http://blog.csdn.net/li_yong_kuan/article/details/52013862