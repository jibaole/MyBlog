---
title: wordpress的nginx设置
tags:
  - nginx
categories:
  - Web server
keywords:
  - wordpress
  - nginx
description:
  - wordpress
  - nginx配置
toc: false
date: 2016-12-28 17:14:01
thumbnail:
banner:
---

```
# Upstream to abstract backend connection(s) for php
upstream php {
        server unix:/var/run/php5-fpm.sock;
        server 127.0.0.1:9000;
}

server {
        ## Your website name goes here.
        server_name www.rails.ren;
        ## Your only path reference.
        root /usr/share/nginx/html/wordpress;
        ## This should be in your http block and if it is, it's not needed here.
        index index.php;

        location = /favicon.ico {
                log_not_found off;
                access_log off;
        }

        location = /robots.txt {
                allow all;
                log_not_found off;
                access_log off;
        }

        if (!-e $request_filename) {
          rewrite /wp-admin$ $scheme://$host$uri/ permanent;
          rewrite ^(/[^/]+)?(/wp-.*) $2 last;
          rewrite ^(/[^/]+)?(/.*\.php) $2 last;
        }
        location / {
                # This is cool because no php is touched for static content.
                # include the "?$args" part so non-default permalinks doesn't break when using query string
          try_files $uri $uri/ /index.php?$args;
  }


        location /wordpress {
                try_files $uri $uri/ /wordpress/index.php?$args;
  }

        location ~ \.php$ {
                #NOTE: You should have "cgi.fix_pathinfo = 0;" in php.ini
                fastcgi_split_path_info ^(/wordpress)(/.*)$;
                include fastcgi.conf;
                fastcgi_intercept_errors on;
                fastcgi_pass php;
        }

        location ~* \.(js|css|png|jpg|jpeg|gif|ico)$ {
                expires max;
                log_not_found off;
        }
}
```

参考：https://www.nginx.com/resources/wiki/start/topics/recipes/wordpress/