---
title: jquery中ajax使用error调试错误的方法
tags:
  - ajax
categories:
  - Web development
keywords:
  - ajax
description:
  - ajax
toc: true
date: 2017-04-26 11:03:28
thumbnail:
banner:
---


#### jquery中ajax的常用用法
``` js
$(document).ready(function() {
  jQuery("#clearCac").click(function() {
 jQuery.ajax({
   url: url,
   type: "post",
   data: { id: '0' },
   dataType: "json",
   success: function(msg) {
 alert(msg);
   },
   error: function(XMLHttpRequest, textStatus, errorThrown) {
 alert(XMLHttpRequest.status);
 alert(XMLHttpRequest.readyState);
 alert(textStatus);
   },
   complete: function(XMLHttpRequest, textStatus) {
 this; // 调用本次AJAX请求时传递的options参数
   }
 });
  });
});
```

#### 当通过ajax异步调用成功时，会调用 success函数
``` js
//请求成功后回调函数。这个方法有两个参数：服务器返回数据，返回状态
 function (data, textStatus)
 {
  // data could be xmlDoc, jsonObj, html, text, etc...   
  this;
 // the options for this ajax request
 }
```

 #### 当通过ajax异步调用出错时，会调用 error函数 
``` js
//(默 认: 自动判断 (xml 或 html)) 请求失败时调用时间。
//参数有以下三个：XMLHttpRequest 对象、错误信息、（可选）捕获的错误对象。
//如果发生了错误，错误信息（第二个参数）除了得到null之外，
//还可能是"timeout", "error", "notmodified" 和 "parsererror"。
 
//textStatus: "timeout", "error", "notmodified" 和 "parsererror"。

error：function (XMLHttpRequest, textStatus, errorThrown) 
{ 
 
} 
```
XMLHttpRequest.readyState: 
0 － （未初始化）还没有调用send()方法
1 － （载入）已调用send()方法，正在发送请求
2 － （载入完成）send()方法执行完成，已经接收到全部响应内容
3 － （交互）正在解析响应内容
4 － （完成）响应内容解析完成，可以在客户端调用了

发送error可能有下面两张引起的，或者其他程序问题，需要我们认真仔细。
1、data:"{}", data为空也一定要传"{}"；不然返回的是xml格式的。并提示parsererror.
2、parsererror的异常和Header 类型也有关系。及编码header('Content-type: text/html; charset=utf8');


原文来自：http://www.jb51.net/article/60881.htm