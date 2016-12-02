---
title: ubuntu14.04下通过apt-get安装jdk
tags:
  - ubuntu
categories:
  - Operating system
toc: true
date: 2015-11-21 09:57:04
description: 
feature:
---

#### Installing default JRE/JDK
``` bash
sudo apt-get update
sudo apt-get install default-jre
sudo apt-get install default-jdk
```

#### Installing Oracle JDK
``` bash
sudo apt-get install python-software-properties
sudo add-apt-repository ppa:webupd8team/java
sudo apt-get update
```

#### Oracle JDK 6(旧版本)
``` bash
sudo apt-get install oracle-java6-installer
```
<!-- more -->
#### Oracle JDK 7(较新的稳定版本)
``` bash
sudo apt-get install oracle-java7-installer
```
#### Oracle JDK 8(最新预览版本)
``` bash
sudo apt-get install oracle-java8-installer
```
#### Managing Java
``` bash
sudo update-alternatives --config java
```

转自：https://segmentfault.com/a/1190000001703180