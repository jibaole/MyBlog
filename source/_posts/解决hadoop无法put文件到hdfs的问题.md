---
title: 解决hadoop无法put文件到hdfs的问题
tags:
  - hadoop
categories:
  - Big Data technology
toc: true
date: 2015-11-14 17:18:17
description: 
  - hadoop
  - hdfs
  - put
keywords:
  - hadoop
  - hdfs
  - put
feature:
---

#### 报错信息
```
org.apache.hadoop.security.AccessControlException: Permission denied: user=emily.ragland, access=WRITE, inode=”staging”:ubuntu:supergroup:rwxr-xr-x
Exception in thread “main” org.apache.hadoop.security.AccessControlException: org.apache.hadoop.security.AccessControlException: Permission denied: user=emily.ragland, access=WRITE, inode=”staging”:ubuntu:supergroup:rwxr-xr-x
```
<!-- more -->
#### 解决办法
* 新增用户
``` shell
ubuntu@ip-172-x-x-x:~$ hadoop fs -mkdir /user/emily.ragland
ubuntu@ip-172-x-x-x:~$ hadoop fs -chown emily.ragland:emily.ragland /user/emily.ragland
```

* 修改hdfs文件所属
```
ubuntu@ip-172-x-x-x:~$ hadoop fs -ls /user/emily.ragland
Found 3 items
drwx------ - emily.ragland emily.ragland 0 2014-03-03 13:02 /user/emily.ragland/.staging
drwxr-xr-x - emily.ragland emily.ragland 0 2014-03-03 13:02 /user/emily.ragland/RESULTS
-rw-r--r-- 1 emily.ragland emily.ragland 12 2014-02-26 12:49 /user/emily.ragland/emily.log
```

