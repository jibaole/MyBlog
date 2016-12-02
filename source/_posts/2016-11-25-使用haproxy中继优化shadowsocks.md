---
title: 使用haproxy中继优化shadowsocks
tags:
  - haproxy
categories:
  - Network proxy
toc: true
date: 2016-11-25 16:59:04
description: 记录使用haproxy做中继来优化shadowsocks
feature:
---

#### 环境
Centos 6.5 64位

#### 安装haproxy
``` bash
yum install haproxy
```

#### 配置haproxy
```
global
        ulimit-n  51200

defaults
        log global
        mode    tcp
        option  dontlognull
        timeout connect 1000ms
        timeout client 150000ms
        timeout server 150000ms

frontend ss-in-us-50000
        bind *:40000
        default_backend ss-out-us-50000

backend ss-out-us-50000
        server server1 us01.logtous.com:50000 maxconn 20480

frontend ss-in-hk-50000
        bind *:40001
        default_backend ss-out-hk-50000

backend ss-out-hk-50000
        server server1 hk01.logtous.com:50000 maxconn 20480
```
<!-- more -->
#### 启动haproxy
``` bash
service haproxy start
chkconfig haproxy on
```

#### 参考资料
https://smileawei.com/shadowsocks-haproxy/
https://github.com/shadowsocks/shadowsocks/wiki/Setup-a-Shadowsocks-relay

