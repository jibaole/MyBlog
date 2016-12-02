---
title: nginx配置优化
tags:
  - nginx
categories:
  - Web server
toc: false
date: 2015-12-14 16:39:07
description:
feature:
---
nginx.conf
``` shell
#运行用户user www-data;
#nginx进程数，建议和CPU总核心数相同worker_processes 4;
pid /run/nginx.pid;
#一个nginx进程打开的最多文件描述符数目，理论值应该是最多打开文件数（系统的值ulimit -n）与nginx进程数相除，但是nginx分配请求并不均匀，所以建议与ulimit -n的值保持一致。worker_rlimit_nofile 65535;
#工作模式与连接数上限events {
    #单个进程最大连接数（最大连接数=连接数*进程数）
    worker_connections 65535;
    #参考事件模型，use [ kqueue | rtsig | epoll | /dev/poll | select | poll ]; epoll模型是Linux 2.6以上版本内核中的高性能网络I/O模型
    use epoll;
    #是否允许Nginx在已经得到一个新连接的通知时，接收尽可能更多的连接。缺省：off
    # multi_accept on;
}
http {
    ##
    # Basic Settings
    ##
    #开启高效文件传输模式，sendfile指令指定nginx是否调用sendfile函数来输出文件，对于普通应用设为 on，
    #如果用来进行下载等应用磁盘IO重负载应用，可设置为off，以平衡磁盘与网络I/O处理速度，降低系统的负载。
    sendfile on;
    #当使用sendfile函数时，tcp_nopush才起作用，它和指令tcp_nodelay是互斥的。
    tcp_nopush on;
    #设置套接字的TCP_NODELAY = on 选项来完成，这样就禁用了Nagle 算法
    tcp_nodelay on;
    #客户端超时时间，这里不是指整个传输过程的时间， 而是指客户端两个读操作之间的时间，即如果客户端超过这么长时间没有读任何数据，nginx关闭该连接
    send_timeout 60;
    #设置http头中的Keep-Alive
    keepalive_timeout 65;
    #set the maximum size of the types hash tables
    types_hash_max_size 2048;
    #隐藏nginx服务器系统版本等信息
    # server_tokens off;
    #指定一个request可接受的body大小,即请求头里的Content-Length. 如果请求body超过该值，nginx返回413("Request Entity Too Large")
    client_max_body_size 10M;
    #客户端请求头部的缓冲区大小，这个可以根据你的系统分页大小来设置，一般一个请求头的大小不会超过1k
    client_header_buffer_size 4k;
    #指定允许为客户端请求头最大分配buffer个数和大小.
    large_client_header_buffers 8 128k;
    # server_names_hash_bucket_size 64;
    # server_name_in_redirect off;
    #文件扩展名与文件类型映射表
    include /etc/nginx/mime.types;
    #默认文件类型
    default_type application/octet-stream;
    ##
    # Logging Settings
    ##
    access_log /var/log/nginx/access.log;
    error_log /var/log/nginx/error.log;
    ##
    # Gzip Settings
    ##
    #开启gzip压缩输出
    gzip on;
    #最小压缩文件大小
    gzip_min_length 1k;
    #压缩缓冲区
    gzip_buffers 16 8k;
    #压缩版本
    gzip_http_version 1.1;
    #压缩等级
    gzip_comp_level 6;
    #ie6 不压缩
    gzip_disable "msie6";
    #Enables response header of "Vary: Accept-Encoding".
    gzip_vary on;
    #Nginx作为反向代理时，启用或关闭压缩上游服务器返回内容的选项
    # gzip_proxied any;
    #压缩类型
    gzip_types text/plain text/css application/json application/x-javascript text/xml application/xml application/xml+rss text/javascript;
    ##
    # Proxy Global Settings
    ##
    #是否开启buffer， 为on时，尽可能从后端读数据存如buffer， 为off时，收到后端数据立即转发给客户端, 对于long-polling应用，需要关闭proxy_buffering
    proxy_buffering on;
    #存放后端服务器返回结果的buffer大小
    proxy_buffer_size 4k;
    #存放后端服务器返回结果的buffer 个数和大小, buffer满时会写到临时文件
    proxy_buffers 8 4k;
    #可以处于busy状态的buffer总和，它控制了同时传输到客户端的buffer数量
    proxy_busy_buffers_size 16k;
    #定义了跟代理服务器连接的超时时间,必须留意这个time out时间不能超过75秒
    proxy_connect_timeout 60s;
    #headers hash table bucket大小，如果headers名称大于64字符，需要增加此值
    proxy_headers_hash_bucket_size 64;
    #headers hash table大小
    proxy_headers_hash_max_size 512;
    proxy_http_version 1.0;
    #指定nginx等待后端返回数据最长时间，该timeout并不是指整个response时间，而是指两次读之间的时间
    proxy_read_timeout 60s;
    #nginx传送请求到后端最大时间，该timeout并不是指整个传输时间，而是指两次写之间的时间
    proxy_send_timeout 30s;
    ##
    # open file optimize
    ##
    #max指定缓存最大文件数，inactive指定缓存失效时间，如在这段时间文件没被下载，移除缓存
    open_file_cache max=102400 inactive=20s;
    #指定多长时间检查一下open_file_cache中文件的有效性
    open_file_cache_valid    60s;
    #指定了在open_file_cache指令无效的参数中一定的时间范围内可以使用的最小文件数， 如果使用更大的值，文件描述符在cache中总是打开状态
    open_file_cache_min_uses 1;
    #是否cache搜索文件的错误
    open_file_cache_errors   on;
    ##
    # Virtual Host Configs
    ##
    include /etc/nginx/conf.d/*.conf;
    include /etc/nginx/sites-enabled/*;
}
```
