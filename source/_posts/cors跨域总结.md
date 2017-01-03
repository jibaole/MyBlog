---
title: cors跨域总结
tags:
  - others
categories:
  - Others
toc: true
date: 2015-11-17 16:34:38
keywords:
  - cors
  - 跨域
description:
  - cors
  - 跨域
feature:
---

#### 一，简单跨域（不带头 不带参数）
要在servlet的doget或者dopost里增加返回头
``` java
resp.addHeader(“Access-Control-Allow-Origin”,”http://zhucetest.duapp.com“); 
```
如果是公共的则返回*即可。

#### 二，复杂跨域

浏览器会先发起一个验证的网络连接到servlet的 doOptions 在doOptions里返回
``` java
resp.addHeader("Access-Control-Allow-Origin","http://zhucetest.duapp.com");
resp.addHeader("Access-Control-Allow-Methods","GET,POST,OPTIONS"); 
resp.addHeader("Access-Control-Allow-Headers", "Content-type,hello");
resp.addHeader("Access-Control-Max-Age", "50");
```
即可
<!-- more -->
#### 三，post传参 加这句话
``` java
xmlHttp.setRequestHeader("Content-Type", "application/x-www-form-urlencoded");
```
#### 四，get传参 直接加在url里

#### 五，servelet接受参数

直接调用req.getparams…
下面是例子
``` java
public class TestOut extends HttpServlet {
 
@Override
protected void doGet(HttpServletRequest req, HttpServletResponse resp)
throws ServletException, IOException {
 
resp.addHeader("Access-Control-Allow-Origin",
"http://zhucetest.duapp.com");
 
 
}
 
@Override
protected void doPost(HttpServletRequest req, HttpServletResponse resp)
throws ServletException, IOException {
 
resp.addHeader("Access-Control-Allow-Origin",
"http://zhucetest.duapp.com");
 
 
}
@Override
protected void doOptions(HttpServletRequest req, HttpServletResponse resp)
throws ServletException, IOException {
 
resp.addHeader("Access-Control-Allow-Origin",
"http://zhucetest.duapp.com");
resp.addHeader("Access-Control-Allow-Methods","GET,POST,OPTIONS"); 
resp.addHeader("Access-Control-Allow-Headers", "Content-type,hello");
 
resp.addHeader("Access-Control-Max-Age", "50");
}
 
}
```

转自：http://yunshangbuhe.iteye.com/blog/1979587
