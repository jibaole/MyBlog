---
title: nginx搭建flv视频点播服务器
tags:
  - nginx
categories:
  - Web server
toc: true
date: 2015-12-14 16:24:00
keywords:
  - mysql
  - nginx
  - flv
description:
  - mysql
  - nginx
  - flv
feature:
---

### 实验目的
让Nginx支持flv和mp4格式文件，同时支持Rtmp协议；同时打开rtmp的hls功能

### 资料
HTTP Live Streaming（缩写是 HLS）是一个由苹果公司提出的基于HTTP的流媒体 网络传输协议。
HLS只请求基本的HTTP报文，与实时传输协议（RTP)不同，HLS可以穿过任何允许HTTP数据通过的防火墙或者代理服务器。它也很容易使用内容分发网络来传输媒体流。
使用ffmpeg来完成对flv、mp4、mp3等格式的转化（点播实验暂时不测试）
<!-- more -->

### 一、准备工作
>模块:nginx_mod_h264_streaming(支持h264编码的视频)
模块:http_flv_module 支持flv
模块:http_mp4_module 支持mp4
下载地址:
http://h264.code-shop.com/download/nginx_mod_h264_streaming-2.2.7.tar.gz
http://nginx.org
https://github.com/arut/nginx-rtmp-module

#### 1、安装依赖包:
``` shell
yum -y install gcc glibc glibc-devel make nasm pkgconfig \ 
lib-devel openssl-devel expat-devel gettext-devel libtool \ 
mhash.x86_64 perl-Digest-SHA1.x86_64
```
#### 2、安装git工具:
``` shell
#mkdir soft-source
#cd soft-source
#wget http://www.codemonkey.org.uk/projects/git-snapshots/git/git-latest.tar.gz
#tar xzvf git-latest.tar.gz 
#cd git-2013-02-04
#autoconf
#./configure
#make && make install
# git --version
git version 1.8.1.GIT
#cd ..
```
#### 3、安装ffmpeg及其依赖包
```
++++++++Yasm+++++++++++
#wget http://www.tortall.net/projects/yasm/releases/yasm-1.2.0.tar.gz
#tar xzvf yasm-1.2.0.tar.gz
#cd yasm-1.2.0
#./configure
#make
#make install
#cd ..
++++++++x264+++++++++++
#git clone git://git.videolan.org/x264
#cd x264
#./configure --enable-shared 
#make
#make install
#cd ..
++++++++LAME+++++++++++
#wget http://downloads.sourceforge.net/project/lame/lame/3.99/lame-3.99.5.tar.gz
#tar xzvf lame-3.99.5.tar.gz
#cd lame-3.99.5
#./configure --enable-nasm
#make
#make install
#cd ..
++++++++libogg+++++++++++
#wget http://downloads.xiph.org/releases/ogg/libogg-1.3.0.tar.gz
#tar xzvf libogg-1.3.0.tar.gz
#cd libogg-1.3.0
#./configure
#make
#make install
#cd ..
++++++++libvorbis+++++++++++
#wget http://downloads.xiph.org/releases/vorbis/libvorbis-1.3.3.tar.gz
#tar xzvf libvorbis-1.3.3.tar.gz
#cd libvorbis-1.3.3
#./configure
#make
#make install
#cd ..
++++++++libvpx+++++++++++
#git clone http://git.chromium.org/webm/libvpx.git
#cd libvpx
#./configure  --enable-shared
#make
#make install
#cd ..
++++++++FAAD2+++++++++++
#wget http://downloads.sourceforge.net/project/faac/faad2-src/faad2-2.7/faad2-2.7.tar.gz
#tar zxvf faad2-2.7.tar.gz
#cd faad2-2.7
#./configure
#make
#make install
#cd ..
++++++++FAAC+++++++++++
#wget http://downloads.sourceforge.net/project/faac/faac-src/faac-1.28/faac-1.28.tar.gz
#tar zxvf faac-1.28.tar.gz
#cd faac-1.28
#./configure
#make
#make install
#cd ..
++++++++Xvid+++++++++++
#wget http://downloads.xvid.org/downloads/xvidcore-1.3.2.tar.gz
#tar zxvf xvidcore-1.3.2.tar.gz
#cd xvidcore/build/generic
#./configure
#make
#make install
cd ..
#git clone git://source.ffmpeg.org/ffmpeg
#cd ffmpeg
#./configure  --prefix=/opt/ffmpeg/ --enable-version3  --enable-libvpx --enable-libfaac --enable-libmp3lame  --enable-libvorbis --enable-libx264 --enable-libxvid --enable-shared --enable-gpl --enable-postproc --enable-nonfree  --enable-avfilter --enable-pthreads
#make && make install
#cd ..
```
修改/etc/ld.so.conf如下:
```
include ld.so.conf.d/*.conf
/lib
/lib64
/usr/lib
/usr/lib64
/usr/local/lib
/usr/local/lib64
/opt/ffmpeg/lib
#ldconfig
```
### 二、安装Nginx相关模块
```
#tar zxvf nginx_mod_h264_streaming-2.2.7.tar.gz
#git clone git://github.com/arut/nginx-rtmp-module.git
#tar zxvf pcre-8.12.tar.gz
#cd pcre-8.12
#./configure
#make && make install
#tar zxvf nginx-1.2.6.tar.gz
#cd nginx-1.2.6
#./configure --prefix=/usr/local/nginx  --add-module=../nginx_mod_h264_streaming-2.2.7 --with-http_flv_module --with-http_gzip_static_module --with-http_stub_status_module --with-http_mp4_module --add-module=../nginx-rtmp-module --add-module=../nginx-rtmp-module/hls --with-cc-opt=-I/opt/ffmpeg/include --with-ld-opt='-L/opt/ffmpeg/lib -Wl,-rpath=/opt/ffmpeg/lib'
#make && make install
```
### 三、修改nginx主配置文件,配置虚拟主机
```
user  nobody nobody;
worker_processes 4;
error_log  logs/nginx_error.log  info;
pid        logs/nginx.pid;
worker_rlimit_nofile 51200;
events
    {
        use epoll;
        worker_connections 51200;
    }
#rtmp_auto_push on;
rtmp {
    server {
    listen 1935;
    application vod {
            play /opt/media/nginxrtmp/flv;
    }
    }
}
http
    {
        include       mime.types;
        default_type  application/octet-stream;
        server_names_hash_bucket_size 128;
        client_header_buffer_size 32k;
        large_client_header_buffers 4 32k;
        client_max_body_size 50m ;
        limit_conn_zone $binary_remote_addr zone=perip:256k;
        limit_conn_log_level notice;
        sendfile on;
        tcp_nopush     on;
        keepalive_timeout 6000;#测试并发临时调大
        tcp_nodelay on;
        gzip on;
        gzip_min_length  1k;
        gzip_buffers     4 16k;
        gzip_http_version 1.0;
        gzip_comp_level 2;
        gzip_types       text/plain application/x-javascript text/css application/xml;
        gzip_vary on;
        #log format
        log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
             '$status $body_bytes_sent "$http_referer" '
             '"$http_user_agent" $http_x_forwarded_for';
#支持flv
server
        {
            listen       8081;
            server_name 192.168.0.33;
            root /opt/pub/media/nginx; #http协议时候，flv视频位置
            location ~ .*.(flv|swf|mp4|wma|wmv)$ {
                    valid_referers none blocked *.xxxx.com http://localhost;
                    if ($invalid_referer) {
                    return 403;
                    }
            }
            location ~ \.flv$ {
                    flv;
                    limit_conn one 20;#限制客户端并发连接数
                    limit_rate 200k;#限制每客户端最大带宽
            }
            location ~ \.mp4$ {
                    flv;
                    limit_conn one 20;
                    limit_rate 200k;
            }
            access_log  logs/nginxflv_access.log  main;
}
server
        {
            listen       8082;
            server_name 192.168.0.33;
            index index.html;
            location / {
                root /opt/pub/media/nginx-rtmp;
            }
            access_log  logs/nginxrtmpflv_access.log  main;
        }
server {
        listen      8080;
        location /stat {
            rtmp_stat all;
            rtmp_stat_stylesheet stat.xsl;
        }
        location /stat.xsl {
            root /opt/pub/soft/nginx-rtmp-module;
        }
        location / {
            root /opt/pub/soft/nginx-rtmp-module/test/rtmp-publisher;
        }
    }
```
http://192.168.0.33:8080/stats 查看rtmp客户请求信息
http://192.168.0.33:8081/index.html 查看nginx http协议时候的flv视频
http://192.168.0.33:8082/index.html  查看nginx rtmp协议时候的flv视频

### 四、基本功能测试
播放器采用开源播放器jwplayer，将播放器放到index.html同级目录下
NGINX-Http测试页面index.html(切记http的时候需要给视频添加关键帧)
``` html
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
<meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
<title>test of nginx-http</title>
</head>
<body>
<div id="myElement"></div>
<object id="FlashID" classid="clsid:D27CDB6E-AE6D-11cf-96B8-444553540000" width="480" height="360">
  <param name="movie" value="player.swf" />
  <param name="quality" value="high" />
  <param name="wmode" value="opaque" />
  <param name="swfversion" value="6.0.65.0" />
  <param name="flashvars" value="type=http&file=http://192.168.0.33:8081/2.flv&autostart=true">
  <EMBED SRC=\'#\'" WIDTH="480" HEIGHT="360" QUALITY="high" PLUGINSPAGE="http://www.macromedia.com/shockwave/download/i
ndex.cgi?P1_Prod_Version=ShockwaveFlash" flashvars="type=http&file=http://192.168.0.33:8081/2.flv&autostart=true" wmode="opaq
ue" /> 
</object>
</body>
</html>
```
NGINX-RTMP测试页面index.html
``` html
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
<meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
<title>nginx-rtmp</title>
</head>
<body>
<div id="myElement"></div>
<object id="FlashID" classid="clsid:D27CDB6E-AE6D-11cf-96B8-444553540000" width="480" height="360">
  <param name="movie" value="player.swf" />
  <param name="quality" value="high" />
  <param name="wmode" value="opaque" />
  <param name="swfversion" value="6.0.65.0" />
  <param name="flashvars" value="streamer=rtmp://192.168.0.33:1935/vod&file=2.flv&autostart=true">
  <EMBED SRC=\'#\'" WIDTH="480" HEIGHT="360" QUALITY="high" PLUGINSPAGE="http://www.macromedia.com/shockwave/download/i
ndex.cgi?P1_Prod_Version=ShockwaveFlash" flashvars="streamer=rtmp://192.168.0.33:1935/vod&file=2.flv&autostart=true" wmode="o
paque" /> 
</object>
</body>
</html>
```
页面能打开正常播放就说明配置是正确的。

### 五、并发测试
NGINX-HTTP采用loadrunner测试（略）
NGINX-RTMP采用开源软件flazr测试:
前提需要安装jdk软件
`client.bat rtmp://192.168.0.33:1935/vod/2.flv -load 500`
这里的500是并发数

由于局域网的限制，带宽测试到一定大小的时候就上不去了，故此步截图不忽略。
借鉴地址:

https://github.com/arut/nginx-rtmp-module/wiki/Examples
http://blog.sina.com.cn/s/blog_438308750100ez27.html
http://space.itpub.net/27043155/viewspace-735288
http://riches.blog.51cto.com/1167414/649598
https://github.com/arut/nginx-rtmp-module
http://www.yeeach.com

转自：http://5iqiong.blog.51cto.com/2999926/1132639
