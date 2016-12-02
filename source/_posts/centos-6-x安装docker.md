---
title: centos 6.x安装docker
tags:
  - docker
categories:
  - Virtualization technology
toc: true
date: 2015-06-16 14:29:10
description: 
feature:
---

>系统内核版本最好在 3.8 以上

#### 1.检查之前是否安装
``` bash
# rpm -qa | grep docker
# yum remove docker
```


#### 2.安装docker
``` bash
# yum install docker-io

```
<!-- more -->
#### 3.测试是否安装成功

``` bash
# docker -d
INFO[0000] +job serveapi(unix:///var/run/docker.sock)
INFO[0000] Listening for HTTP on unix (/var/run/docker.sock)
INFO[0000] WARNING: You are running linux kernel version 2.6.32-504.8.1.el6.x86_64, which might be unstable running docker. Please upgrade your kernel to 3.8.0.
docker: relocation error: docker: symbol dm_task_get_info_with_deferred_remove, version Base not defined in file libdevmapper.so.1.02 with link time reference
# yum upgrade device-mapper-libs
# docker pull centos
centos:latest: The image you are pulling has been verified
511136ea3c5a: Pull complete
5b12ef8fd570: Pull complete
88f9454e60dd: Pull complete
Status: Downloaded newer image for centos:latest
# docker run -i -t –rm centos /bin/echo “hello world”
hello world
```

参考：http://supertaodi.blog.51cto.com/7256234/1619603