---
title: codeigniter在nginx安装配置及url重写
tags:
  - ci
categories:
  - Web development
toc: false
date: 2015-09-14 16:20:43
description: ci是一个轻量型的PHP优秀框架，但是它是在apache服务器下开发的，在nginx下需要特别的配置才可以使用。
feature:
---

对application/config/config.php进行修改：
```
$config['uri_protocol'] = "PATH_INFO";

```
大约在48行左右

```
server {
        listen       80;
        listen [::]:80 ipv6only=on;
        server_name  www.example.com;
 
        root   /data/www/www.example.com;
        index index.php  index.html index.htm;
 
        location / {
                # 这里使用try_files进行url重写，不用rewrite了。
                try_files $uri $uri/ /index.php?$query_string;
        }
 
        location ~ \.php($|/) {
            fastcgi_pass   127.0.0.1:9000;
            fastcgi_index  index.php;
            fastcgi_split_path_info ^(.+\.php)(.*)$;
            fastcgi_param   PATH_INFO $fastcgi_path_info;
            fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
            include        fastcgi_params;
        }
 
        location ~ /\.ht {
                deny  all;
        }
}
```
注意19行的include fastcgi_params;，如果没有这一行，那么你的PHP程序会无法运行的。