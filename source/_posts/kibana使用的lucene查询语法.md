---
title: kibana使用的lucene查询语法
tags:
  - kibana
categories:
  - Big Data technology
toc: true
date: 2016-04-15 16:24:35
description: 
feature:
---

#### 全文搜索
在搜索栏输入login，会返回所有字段值中包含login的文档
![](https://segmentfault.com/image?src=http://ww3.sinaimg.cn/large/005UHBLCgw1etvgt13cpfj31hc0saasc.jpg&objectId=1190000002972420&token=c5722ce23f803369898b45312196563c)
<!-- more -->

#### 短语搜索
使用双引号包起来作为一个短语搜索
`“like Gecko”`

#### 字段
也可以按页面左侧显示的字段搜索
限定字段全文搜索：field:value
精确搜索：关键字加上双引号 filed:”value”
http.code:404 搜索http状态码为404的文档

字段本身是否存在
exists:http：返回结果中需要有http字段
missing:http：不能含有http字段

#### 通配符
? 匹配单个字符

匹配0到多个字符
kiba?a, el*search

? 不能用作第一个字符，例如：?text text

#### 正则
es支持部分正则功能
mesg:/mes{2}ages?/

#### 模糊搜索
~:在一个单词后面加上~启用模糊搜索

first~ 也能匹配到 frist

还可以指定需要多少相似度
cromm~0.3 会匹配到 from 和 chrome
数值范围0.0 ~ 1.0，默认0.5，越大越接近搜索的原始值

#### 近似搜索
在短语后面加上~
“select where”~3 表示 select 和 where 中间隔着3个单词以内

#### 范围搜索
数值和时间类型的字段可以对某一范围进行查询
length:[100 TO 200]
date:{“now-6h” TO “now”}
[ ] 表示端点数值包含在范围内，{ } 表示端点数值不包含在范围内


#### 逻辑操作
AND
OR

+：搜索结果中必须包含此项
-：不能含有此项
+apache -jakarta test：结果中必须存在apache，不能有jakarta，test可有可无

#### 分组
(jakarta OR apache) AND jakarta

#### 字段分组
title:(+return +”pink panther”)

#### 转义特殊字符
&& || ! () {} [] ^” ~ * ? : \
以上字符当作值搜索的时候需要用\转义

转自：https://segmentfault.com/a/1190000002972420