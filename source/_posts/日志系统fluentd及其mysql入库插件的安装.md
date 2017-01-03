---
title: 日志系统fluentd及其mysql入库插件的安装
tags:
  - fluentd
categories:
  - Big Data technology
toc: true
date: 2016-03-11 10:54:16
description: 
  - fluentd
  - mysql
keywords:
  - fluentd
  - mysql
feature:
---
### 安装步骤

#### 1.安装gem
```
yum -y install gcc mysql-devel ruby-devel rubygems
```

#### 2.修改gem source
```
gem sources --add https://ruby.taobao.org/ --remove http://rubygems.org/

```

#### 3.安装fluentd之后通过下面命令安装mysql驱动
```
/usr/sbin/td-agent-gem install mysql2
```

#### 4.安装mysql入库插件
```
/usr/sbin/td-agent-gem install fluent-plugin-mysql-load
```


### 补充资料
#### 1.解决load data入库违反唯一索引入库报错问题
修改文件`/opt/td-agent/embedded/lib/ruby/gems/2.1.0/gems/fluent-plugin-mysql-load-0.0.2/lib/fluent/plugin/out_mysql_load.rb`
原来：
```
QUERY_TEMPLATE = "LOAD DATA LOCAL INFILE '%s' INTO TABLE %s (%s)"
```

修改后：
```
QUERY_TEMPLATE = "LOAD DATA LOCAL INFILE '%s' IGNORE INTO TABLE %s (%s)"
```

#### 2.解决重复入库小技巧
* 复制表结构，并对新表增加唯一索引
* 通过group by 唯一索引并insert到新表
* 将新表表名修改为原来表名

#### 3.fluentd版本
```
rpm -ivh td-agent-2.2.0-0.x86_64.rpm
```

#### 4.如果遇到无法安装mysql2驱动的问题，可以尝试使用如下方式解决
```
rpm -ivh http://mirrors.sohu.com/mysql/MySQL-5.6/MySQL-shared-5.6.30-1.el6.x86_64.rpm

```




