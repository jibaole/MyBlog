---
title: centos上配置lnmp
tags:
  - nginx
categories:
  - Web server
toc: true
date: 2014-12-14 10:37:56
keywords:
  - centos
  - lnmp
description:
  - centos
  - lnmp
feature:
---

### 配置nginx
#### 1. 启动nginx服务
```
[root@VM_185_51 /]# service nginx restart
```

#### 2. 测试nginx服务是否正常运行
如果最后显示：`index.html’ saved ，说明nginx服务正常。
```
[root@VM_185_51 /]# wget http://127.0.0.1
–2013-02-20 17:07:26– http://127.0.0.1/
Connecting to 127.0.0.1:80… connected.
HTTP request sent, awaiting response… 200 OK
Length: 151 [text/html]
Saving to: `index.html’
100%[==========================================================================================>] 151 –.-K/s in 0s
2013-02-20 17:07:26 (37.9 MB/s) – `index.html’ saved [151/151]
```
<!-- more -->
### 配置PHP
#### 1. 启动php-fpm
```
[root@VM_185_51 /]# service php-fpm start
```

#### 2. 修改php-fpm和nginx的配置，实现nginx和php联动
（1）查看php-fpm默认配置
```
[root@VM_185_51 /]# cat /etc/php-fpm.d/www.conf |grep -i ‘listen =’
listen = 127.0.0.1:9000
```
（2）php-fpm的默认配置的监听端口为9000，现在只用修改配置，将php解析的请求转发到127.0.0.0:9000处理即可
```
[root@VM_185_51 /]# vim /etc/nginx/conf.d/default.conf

```
修改完成后实际配置如下，红色文字为修改过的部分：
```
server {
  listen       80;
  root   /usr/share/nginx/html;
  server_name  localhost;
  #charset koi8-r;
  #access_log  /var/log/nginx/log/host.access.log  main;
  location / {
      index  index.html index.htm;
  }
  #error_page  404              /404.html;
  # redirect server error pages to the static page /50x.html
  #
  error_page   500 502 503 504  /50x.html;
  location = /50x.html {
      root   /usr/share/nginx/html;
  }
  # proxy the PHP scripts to Apache listening on 127.0.0.1:80
  #
  #location ~ .php$ {
  #    proxy_pass   http://127.0.0.1;
  #}
  # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
  #
  location ~ .php$ {
      fastcgi_pass   127.0.0.1:9000;
      fastcgi_index   index.php;
      fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
      include        fastcgi_params;
  }
  # deny access to .htaccess files, if Apache's document root
  # concurs with nginx's one
  #
  #location ~ /.ht {
  #    deny  all;
  #}
}
```
修改完成后，按“Esc”键，输入“:wq”，保存文件并返回。
通过下面的命令，查看配置是否正确：
```
[root@VM_185_51 /]# cat /etc/nginx/conf.d/default.conf

```

#### 3. 配置生效重启nginx，使配置生效
```
[root@VM_185_51 /]# service nginx restart
Stopping nginx: [ OK ]
Starting nginx: [ OK ]

```

### 环境配置验证

#### 1. 在web目录下创建index.php
```
[root@VM_185_51 /]# vim /usr/share/nginx/html/index.php

```

index.php文件的内容如下：
```
<?php
echo “<title>Test Page</title>”;
echo “hello world”;
?>
```