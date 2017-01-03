---
title: gravatar被墙导致图片无法显示的四种解决办法
tags:
  - others
categories:
  - Others
toc: false
date: 2015-11-17 11:02:56
keywords:
  - gravatar
  - 被墙
  - 图片
description:
  - gravatar
  - 被墙
  - 图片
feature:
---

* 使用未被墙的 HTTPS 链接Gravatar 官方一共有 5 个地址，其中前 4 个被墙了，所以我们可以统一替换为未被墙的那个地址：
http://www.gravatar.com
http://0.gravatar.com
http://1.gravatar.com
http://2.gravatar.com
https://secure.gravatar.com
添加以下代码到 functions.php：
``` php
function unblock_gravatar( $avatar ) {
$avatar = str_replace( array( ‘http://www.gravatar.com‘, ‘http://0.gravatar.com‘, ‘http://1.gravatar.com‘, ‘http://2.gravatar.com‘ ), ‘https://secure.gravatar.com‘, $avatar );
return $avatar;}add_filter( ‘get_avatar’, ‘unblock_gravatar’ );
```
<!-- more -->
* 使用多说提供的镜像地址多说是国内 No.1 的社会化评论系统，它专门设立了一个镜像地址来中转显示 Gravatar，加载速度可比官方的快多了，代码和方法 1 类似：
``` php
function unblock_gravatar( $avatar ) {
$avatar = str_replace( array( ‘www.gravatar.com’, ‘0.gravatar.com’, ‘1.gravatar.com’, ‘2.gravatar.com’ ), ‘gravatar.duoshuo.com’, $avatar );
return $avatar;}add_filter( ‘get_avatar’, ‘unblock_gravatar’ );
```
* 使用七牛云存储的镜像功能本站曾介绍过如何使用七牛云存储来加速 WordPress，同样的方法，你也用来加速 Gravatar，本方法稍显麻烦，就不具体展开了。
大致就是建一个七牛空间来镜像 gravatar.com，然后借用方法 2 替换 gravatar.com 为七牛的域名。

* 使用 WordPress 插件缓存头像到主机空间如果你使用的是国外主机，可以正常访问 Gravatar.com，那么你也可以选择将 Gravatar 缓存到自己的主机空间。

转自：https://www.janecc.com/unblock-gravatar.html