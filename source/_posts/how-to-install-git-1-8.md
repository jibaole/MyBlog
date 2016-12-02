---
title: how to install git 1.8
tags:
  - git
categories:
  - Version control
toc: true
date: 2015-11-16 14:19:47
description:
feature:
---
#### 1.Download PUIAS repo
``` bash
wget -O /etc/yum.repos.d/PUIAS_6_computational.repo https://gitlab.com/gitlab-org/gitlab-recipes/raw/master/install/centos/PUIAS_6_computational.repo

```


#### 2.Next download and install the GPG key
``` bash
wget -O /etc/pki/rpm-gpg/RPM-GPG-KEY-puias http://springdale.math.ias.edu/data/puias/6/x86_64/os/RPM-GPG-KEY-puias && rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-puias
```
<!-- more -->

#### 3.Verify that the key was installed successfully
``` bash
rpm -qa gpg*
```

#### 4.Verify that the EPEL and PUIAS Computational repositories are enabled
``` bash
yum repolist

```

#### 5.Install Git 1.8
``` bash
yum --enablerepo=PUIAS_6_computational install git

```