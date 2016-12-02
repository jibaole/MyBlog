---
title: 在centos上面安装svn 1.8.x版本
tags:
  - svn
categories:
  - Version control
description: 参考:http://opensource.wandisco.com/
toc: true
date: 2015-11-09 17:13:34
feature:
---

#### 1.安装yum repo
```
touch /etc/yum.repos.d/wandisco-svn.repo
```
然后将下面内容填写在上面创建的repo文件中
```
[WandiscoSVN]
name=Wandisco SVN Repo
baseurl=http://opensource.wandisco.com/centos/$releasever/svn-1.8/RPMS/$basearch/
enabled=1
gpgcheck=0
```
#### 2.安装svn 1.8版本
<!-- more -->
安装之前先将已经存在svn相关联文件卸载
```
# yum remove subversion*
```
现在开始安装新版本的svn
```
# yum clean all
# yum install subversion
```
#### 3.查看svn版本
```
# svn --version
svn, version 1.8.15 (r1718365)
  compiled Dec 11 2015, 14:28:48 on x86_64-redhat-linux-gnu
```


