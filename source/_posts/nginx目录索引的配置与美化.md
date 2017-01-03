---
title: nginx目录索引的配置与美化
tags:
  - nginx
categories:
  - Web server
toc: true
date: 2015-11-16 16:45:00
keywords:
  - nginx
  - 目录索引
description:
  - nginx
  - 目录索引
feature:
---

#### 1.安装Nginx，并加入FancyIndex插件
FancyIndex是一个美化插件，可以引入自定义HTML内容用于美化索引页面。如果仅仅需要开启目录浏览而不需要美化，可以不引入此插件。
``` bash
$ yum install gcc gcc-c++ make openssl openssl-devel
```
``` bash
$ wget http://sourceforge.net/projects/pcre/files/pcre/8.33/pcre-8.33.tar.gz
$ tar -zxvf pcre-8.33.tar.gz
$ cd pcre-8.33
$ ./configure
$ make && make install
$ cd ../
```
<!-- more -->
``` bash
$ groupadd www
$ useradd -s /sbin/nologin -g www www
$ git clone https://github.com/aperezdc/ngx-fancyindex.git ngx-fancyindex
```
``` bash
$ wget http://nginx.org/download/nginx-1.6.0.tar.gz
$ tar zxvf nginx-1.6.0.tar.gz
$ cd nginx-1.6.0/
$ ./configure --user=www --group=www --prefix=/usr/local/nginx --with-http_stub_status_module --with-http_ssl_module --with-http_gzip_static_module --add-module=../ngx-fancyindex
$ make && make install
$ ln -s /usr/local/nginx/sbin/nginx /usr/bin/nginx
```
``` bash
$ git clone https://github.com/ixbear/nginx
$ mv nginx/nginx.conf /usr/local/nginx/conf/
$ mv nginx/init.d.nginx /etc/init.d/nginx
$ chmod +x /etc/init.d/nginx
$ chkconfig --level 345 nginx on
$ /etc/init.d/nginx start
```

#### 2.打开Nginx索引功能，并美化索引页面
``` bash
$ vim /usr/local/nginx/conf/nginx.conf  #作如下修改
server
  {
    listen       80;
    server_name www.lnmp.org;
    index index.html index.htm index.php;
    root  /home/wwwroot;
    autoindex on;         #打开索引功能    autoindex_exact_size off;  #人性化方式显示大小    autoindex_localtime on;    #显示服务器时间    fancyindex on;       #开启美化    fancyindex_exact_size off;
    fancyindex_header /.header.html;
    fancyindex_footer /.footer.html;
```
根据如上配置文件，可以看出网站的根目录是/home/wwwroot。因此我们需要在根目录建立两个文件：header.html和footer.html。
``` bash
$ vim /home/wwwroot/.header.html  #输入如下内容
<html xmlns="http://www.w3.org/1999/xhtml"><head><meta http-equiv="content-type" content="text/html; charset=utf-8"/><style type="text/css" media="screen">body,html {background:#fff;font-family: "Lucida Grande",Calibri,Arial;font-size: 13pt;color: #333;background: #f8f8f8;}tr.e {background:#f4f4f4;}th,td {padding:0.1em 0.5em;}th {text-align:left;font-weight:bold;background:#eee;border-bottom:1px solid #aaa;}#top1 {width:80%; font-size:28px; margin: 0 auto 5px auto;}#top2 {width:80%; font-size:18px; margin: 0 auto 5px auto;}#footer {width:80%;margin: 0 auto; padding: 10pt 0;font-size: 10pt;text-align:center;}#footer a {font-size: 14px; font-weight: normal; text-decoration: underline;}#list {border:1px solid #aaa;width:80%;margin: 0 auto;padding: 0;}a {color: #b00;font-size: 11pt;font-weight: bold;text-decoration: none;}a:hover {color: #000;}#readme {padding:0;margin:1em 0;border:none;width:100%;}</style><script type="text/javascript">// <![CDATA[function ngx_onload(){var f=document.getElementById('readme');if(!(f&&f.contentDocument))return;f.style.height=f.contentDocument.body.offsetHeight+'px';f.contentDocument.body.style.padding='0';f.contentDocument.body.style.margin='0';}// ]]></script><title>Inspur Linux OS Download Center</title></head><body onload="ngx_onload()"><h1 id="top1">Inspur Linux OS Download Center</h1><h1 id="top2">Directory listing of
$ vim /home/wwwroot/.footer.html  #输入如下内容
<table id="footer" cellpadding="0" cellspacing="1"><thead><tr><td colspan="3">本页面由系统软件部研发二处负责维护</td></tr><thead></table></body></html>
```


#### 3.为目录配置密码保护
根据上一步骤中的配置文件，可以看出网站的根目录是/home/wwwroot。假设网站根目录下有一个名为repo的隐私目录，下面我们为此隐私目录添加密码验证。
``` bash
$ vim /usr/local/nginx/conf/nginx.conf  #作如下修改
server
  {
    listen       80;
    server_name www.lnmp.org;
    index index.html index.htm index.php;
    root  /home/wwwroot;
    autoindex on;
    autoindex_exact_size off;
    autoindex_localtime on;
    fancyindex on;
    fancyindex_exact_size off;
    fancyindex_header /.header.html;
    fancyindex_footer /.footer.html;
    location /repo {
      auth_basic "input you user name and password";
      auth_basic_user_file /home/wwwroot/repo/.htpasswd;
    }
```
其中，auth_basic是弹出的文字提示，而.htpasswd则是记录登陆用户名与密码的文件。该文件可用Apache的htpasswd工具创建。 查看创建.htpasswd文件的方法 。

转自：http://www.zhukun.net/archives/7343