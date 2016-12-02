---
title: how to set up epel repository on centos
tags:
categories:
  - Linux server
toc: true
date: 2015-11-14 10:33:45
description:
feature:
---

If you are using CentOS or RHEL, it is strongly recommended that you configure EPEL (Extra Packages for Enterprise Linux) repository on your system. EPEL is a community effort to create a repository of high-quality add-on free software packages for RHEL-based distributions. Once you set up EPEL repository, you can use yum command to install any of close to 7,000 EPEL packages.
In order to enable EPEL repository on your CentOS system, you need to check CentOS version. To do that, run the following command.
```
$ cat /etc/redhat-release
CentOS release 6.3 (Final)
```
<!-- more -->
Then install a corresponding EPEL release RPM package as follows. Note that the EPEL release RPM does not depend on the underlying processor architecture (e.g., 32-bit/64-bit x86, ppc, sparc, alpha, etc), so no need to pay attention to processor architecture difference.
Set up EPEL on CentOS 7Starting from CentOS 7, EPEL release RPM package is available in “extras” repo. Therefore, simply use yum command to set up EPEL repository on these platforms:
```
$ sudo yum install epel-release
```
Set up EPEL on CentOS 6 or EarlierFor earlier versions of CentOS, you can use rpm command to download and install a RPM file manually as follows.
`For CentOS/RHEL 6.*:`
```
$ sudo rpm -Uvh http://mirrors.kernel.org/fedora-epel/6/i386/epel-release-6-8.noarch.rpm
```
`For CentOS/RHEL 5.*:`
```
$ sudo rpm -Uvh http://mirrors.kernel.org/fedora-epel/5/i386/epel-release-5-4.noarch.rpm
```
During installation, you may see the following warning, which indicates that EPEL’s GPG key is missing.
warning: /var/tmp/rpm-tmp.3TKM2G: Header V3 RSA/SHA256 Signature, key ID 0608b895: NOKEY

The EPEL’s official GPG key is found in /etc/pki/rpm-gpg. Go ahead and import the GPG key as follows.
```
$ sudo sudo rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-EPEL-6 (for CentOS 6)
$ sudo sudo rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-EPEL-7 (for CentOS 7)
```
To verify that EPEL repository has been set up successfully, run the following command to list all available repositories on your system.
```
$ yum repolist
```

