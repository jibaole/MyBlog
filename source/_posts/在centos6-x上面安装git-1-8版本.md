---
title: 在centos6.x上面安装git 1.8版本
categories:
  - Version control
toc: true
date: 2016-09-08 11:39:32
tags:
  - git
description: 记录在centos6.5中安装git 1.8版本的操作步骤
feature:
---

### 安装步骤

#### 1.下载PUIAS repo
```
wget -O /etc/yum.repos.d/PUIAS_6_computational.repo https://gitlab.com/gitlab-org/gitlab-recipes/raw/master/install/centos/PUIAS_6_computational.repo

```


#### 2.下载和安装PUIAS对应的GPG key
```
wget -O /etc/pki/rpm-gpg/RPM-GPG-KEY-puias http://springdale.math.ias.edu/data/puias/6/x86_64/os/RPM-GPG-KEY-puias && rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-puias
```

#### 3.检查GPG key是否安装成功
```
rpm -qa gpg*
```

#### 4.检查PUIAS repo是否已经加载
```
yum repolist
```

#### 5.安装git 1.8版本
```
yum --enablerepo=PUIAS_6_computational install git
```
