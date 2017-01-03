---
title: apache禁止显示目录索引的方法
tags:
  - apache
categories:
  - Web server
toc: true
date: 2015-04-11 11:30:18
keywords:
  - apache
  - 目录索引
  - 禁止
description:
  - apache
  - 目录索引
  - 禁止
feature:
---

### 三种方法

#### 1.修改目录配置
```
<Directory "D:/Apache/blog.phpha.com">
Options Indexes FollowSymLinks # 修改为: Options  FollowSymLinks
    AllowOverride None
    Order allow,deny
    Allow from all
</Directory>
```
<!-- more -->
#### 2.修改Apache配置文件httpd.conf
搜索`Options Indexes FollowSymLinks`，修改为`Options -Indexes FollowSymLinks`即可。在`Options Indexes FollowSymLinks`在Indexes前面加上 – 符号。

备注：在Indexes前，加 + 代表允许目录浏览；加 – 代表禁止目录浏览

如果是虚拟机配置，可以做修改修改来实现：
```
<VirtualHost *>
    <Directory "../vhosts/blog.phpha.com">
        Options -Indexes FollowSymLinks # 修改为 -Indexes 即可
    </Directory>
    ServerAdmin mail@jb51.com
    DocumentRoot "../vhosts/blog.phpha.com"
    ServerName shopex:80
    ServerAlias blog.phpha.com
    ErrorLog logs/blog.phpha.com-error_log
</VirtualHost>
```

#### 3.通过.htaccess文件
在根目录新建或修改 .htaccess 文件中添加
```
<Files *>
 Options -Indexes
</Files>

```
