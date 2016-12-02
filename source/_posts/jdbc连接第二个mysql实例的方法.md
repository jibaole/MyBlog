---
title: jdbc连接第二个mysql实例的方法
tags:
  - mysql
categories:
  - Database
toc: true
date: 2015-01-11 11:28:32
description: 记录jdbc连接第二个实例的实现方法
feature:
---

#### jdbc连接mysql的url格式
```
jdbc:mysql://[host][,failoverhost...][:port]/[database] »
[?propertyName1][=propertyValue1][&propertyName2][=propertyValue2]...

```
如果主机未制定，则默认是`127.0.0.1`；如果端口未指定，则默认是`3306`.
```
jdbc:mysql://[host:port],[host:port].../[database] »
[?propertyName1][=propertyValue1][&propertyName2][=propertyValue2]...

```
#### jdbc连接mysql的样例
```
jdbc:mysql://localhost:3306/sakila?profileSQL=true

```