---
title: html5本地存储之localstorage、本地数据库、sessionstorage的使用示例
tags:
  - html5
categories:
  - Frontend technology
toc: true
date: 2016-08-18 11:16:52
keywords:
  - html5
  - 本地存储
  - localstorage
  - sessionstorage
description:
  - html5
  - 本地存储
  - localstorage
  - sessionstorage
feature:
---

>这篇文章主要介绍了html5本地存储的localstorage 、本地数据库、sessionStorage简单使用示例,需要的朋友可以参考下
html5的一个非常cool的功能，就是web storage，类似于之前的cookie，不过与之不同的是，web storage 拥有本地5兆的容量可以存储，而cookie却只有4K，这是完全不能比的优势。
webstrange又分为：localstorage，sessionstorage和本地数据库。

接下来我就来一一介绍：

#### 1、localstorage 
localstorage 的使用比较简单，方法有：
``` js

localStorage.setItem(key,value);//保存数据
localStorage.getItem(key);//读取数据
localStorage.removeItem(key);//删除单个数据
localStorage.clear();//删除所有数据
key：localStorage.key(index);//得到某个索引的值
```
<!-- more -->
一个小demo来展示功能：
``` js
(function($){
$(function(){
$.fn.getFormParam=function(){
var serializeObj={};
var array=this.serializeArray();
var str=this.serialize();
$(array).each(function(){
if(serializeObj[this.name]){
if($.isArray(serializeObj[this.name])){
serializeObj[this.name].push(this.value);
}else{
serializeObj[this.name]=[serializeObj[this.name],this.value];
}
}else{
serializeObj[this.name]=this.value;
}
});
return serializeObj;
};</p> <p> var storageFile =JSON.parse(window.localStorage.getItem('demo'));
$.each(storageFile, function(i, val){
$('#demoForm').find('[name="'+i+'"]').val(val);
});</p> <p> $('#demoForm').find('[type="submit"]').on('click', function(){
var data = $('#demoForm').getFormParam();
window.localStorage.setItem('demo', JSON.stringify(data));
return false;
});
});
})(jQuery)
```
html 代码：
``` html
<!doctype html>
<html lang="zh">
<head>
<meta charset="UTF-8">
<script src="jquery-1.10.2.min.js"></script>
<script src="demo.js"></script>
<title>Document</title>
</head>
<body>
<form id="demoForm">
<p><label><span>姓名</span><input name="name"></label></p>
<p><label><span>年龄</span><input name="age"></label></p>
<p><label><span>学号</span><input name="number"></label></p>
<p><label><span>地址</span><input name="address"></label></p>
<p><label><span>爱好</span><input name="habit"></label></p>
<p><label><span>其他</span><textarea name="big" id="" cols="30" rows="10"></textarea></label></p>
<p><input type="submit" value="提交"></p>
</form>
</body>
</html>
```
这样，一个简单的展示localstorage 的 demo就实现了

#### 2、sessionStorage
sessionStorage用法与localStorage用法相同，不过sessionStorage在浏览器关闭网站时候就会清除，而localStorage会一直保存至浏览器中，二者酌情配合使用。

#### 3、本地数据库
熟悉IOS/Android开发的同学，应该会对SQLite数据库比较熟悉
html5中对数据库的操作比较简单，主要有openDatabase方法和transaction方法
用一个对象db来接收openDatabase创建的访问数据库的对象
``` js
var db = openDatabase(databasename,version,description,size)
```
其中
databasename:数据库名
version：数据库版本 可不填
desription：数据库描述
size：数据库分配空间大小
 
transaction方法用一个回调函数作为参数，在函数中执行具体的访问数据库的方法
``` js
db.transaction(function(tx)){
tx.executeSql(sqlQuery,[value1,value2..],dataHandler,errorHandler)
});
```
executeSql方法的四个参数分别是：
sqlQuery：需要具体执行的sql语句，create||select||update||delete；
[value1,value2..]：sql语句中所有使用到的参数的数组，在executeSql方法中，将sql语句中所要使用的参数先用“?”代替，然后依次将这些参数组成数组放在第二个参数中；
dataHandler：执行成功回调函数；
errorHandler：执行失败回调函数；

转自：http://www.jb51.net/html5/163365.html