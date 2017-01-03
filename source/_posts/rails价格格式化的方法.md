---
title: rails价格格式化的方法
tags:
  - rails
categories:
  - Web development
toc: true
date: 2016-08-18 11:11:24
keywords:
  - rails
  - 价格
  - 格式化
description:
  - rails
  - 价格
  - 格式化
feature:
---

#### 一种是直接在试图中队价格信息进行格式化
如：
``` html
<p class="price">
<%=sprintf("￥%0.02f",product.price)%>
</p>
```

#### 另外一种是用单独的辅助方法来处理价格格式化，
即number_to_currency，
如：
``` html
<p class="price">
    <%=number_to_currency(product.price)%>
</p>
```
显示结果为$100.00
<!-- more -->
``` html
<p class="price">
    <%=number_to_currency(product.price,  :unit=>"￥")%>
</p>
```
显示结果为￥100.00

#### 完整描述为number_to_currency(number,options={}),
将数字格式化为金额字符串，
options是一个hash，用于定制输出格式
包含:precesion    指定数字的精度，默认为2
    :unit         指定货币输出格式,默认为$
    :separator    指定整数与小数之间的分隔符,默认为 "."
    :delimiter    指定整数部分的定界符
示例：
``` ruby
number_to_currency(1234567890.123,:unit=>"￥",:separator=>",",:delimiter=>".")
->1.234.567.890,123
```
