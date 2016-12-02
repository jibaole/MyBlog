---
title: cannot connect to the docker daemon报错的解决办法
tags:
  - docker
categories:
  - Virtualization technology
toc: true
date: 2015-06-18 15:13:38
description: 
feature:
---

### Docker Daemon Configuration Files
This section covers Docker related files and directory permissions and ownership. Keeping the files and directories, that may contain sensitive parameters, secure is important for correct and secure functioning of Docker daemon.

#### Verify that docker.socket file ownership is set to root:root
If you are using Docker on a machine that uses systemd to manage services, then verify that the ‘docker.service’ file ownership and group-ownership is correctly set to ‘root’.So that when the account is switched to test user he is not able to access the docker daemon as he is not authorized to do so by root account.
``` bash
$ stat -c %U:%G /usr/lib/docker | grep -v root:root
$ stat -c %U:%G /usr/lib/docker | grep root:root
root:root
```
<!-- more -->
If the permission is not set to root:root then it can be changed by using the following command;
``` bash
$ chown root:root /usr/lib/systemd/system/docker.service
$ su test
$ docker ps
```
Cannot connect to the Docker daemon. Is the docker daemon running on this host?
test@ubuntu:/etc/init.d$
#### Verify that docker.socket file permissions are set to 644 or more restrictive 660
If you are using Docker on a machine that uses systemd to manage services, then verify that the ‘docker.service’ file permissions are correctly set to ‘644’ or more restrictive.

As it can be seen below if we allocate 666 as the permission then the “test” user will be also be available to access the Docker daemon;
``` bash
$ ls -l /var/run/docker.sock
srw-rw---- 1 root docker 0 Nov 27 05:56 /var/run/docker.sock
$ chmod 666 /var/run/docker.sock
$ su test
test@ubuntu:/etc/init.d$ docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
test@ubuntu:/etc/init.d$
As soon as we change the permission to 660 we will be able to see that the “test” user is not able to access the docker daemon.

$ chmod 660 /var/run/docker.sock
$ su test
test@ubuntu:/etc/init.d$ docker ps
Cannot connect to the Docker daemon. Is the docker daemon running on this host?
test@ubuntu:/etc/init.d$ exit
exit
```

转自：http://www.cnblogs.com/rexzhao/p/5048004.html