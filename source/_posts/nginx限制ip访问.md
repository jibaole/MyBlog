---
title: nginx限制ip访问
tags:
  - nginx
categories:
  - Web server
toc: true
date: 2015-04-15 16:34:50
description:
feature:
---

#### 站点限IP
``` conf
vi vhosts.conf
#站点全局限IP:
location / {
    index  index.html index.htm index.php;
    allow 10.57.22.172;
    deny all;
    location ~ \.php$ {
        if ( $fastcgi_script_name ~ \..*\/.*php ) {return 403;}
        fastcgi_pass   127.0.0.1:9000;
        fastcgi_index  index.php;
        fastcgi_param  SCRIPT_FILENAME  /var/www/htdocs$fastcgi_script_name;
        include fastcgi_params;
    }
}
#站点IP限目录:
location / {
    index  index.html index.htm index.php;
}
location ^~ /test/ {
    allow 10.57.22.172;
    deny all;
    if ( $fastcgi_script_name ~ \..*\/.*php ) {return 403;}
    fastcgi_pass   127.0.0.1:9000;
    fastcgi_index  index.php;
    fastcgi_param  SCRIPT_FILENAME  /var/www/htdocs$fastcgi_script_name;
    include fastcgi_params;
}
location ~ \.php$ {
    if ( $fastcgi_script_name ~ \..*\/.*php ) {return 403;}
    fastcgi_pass   127.0.0.1:9000;
    fastcgi_index  index.php;
    fastcgi_param  SCRIPT_FILENAME  /var/www/htdocs$fastcgi_script_name;
    include fastcgi_params;
}
```
<!-- more -->
#### 注意事项
* deny 一定要加一个ip，否则直接跳转到403,不往下执行了;如果403默认页是同一域名下，会造成死循环访问
* allow的ip段：
从允许访问的段位从小到大排列,如127.0.0.0/24 下面才能是10.10.0.0/16
24表示子网掩码:255.255.255.0
16表示子网掩码:255.255.0.0
8表示子网掩码:255.0.0.0
* deny all;结尾 表示除了上面allow的其他都禁止，如：
```
deny 192.168.1.1;
allow 127.0.0.0/24;
allo w 192.168.0.0/16;
allow 10.10.0.0/16;
deny all;
```