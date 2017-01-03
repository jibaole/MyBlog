---
title: linux下安装redmine3.2
tags:
  - redmine
categories:
  - Automated software
toc: true
date: 2015-05-15 14:31:52
keywords:
  - linux
  - redmine
description:
  - linux
  - redmine
feature:
---

#### 环境准备
>提前安装好ruby、rails


#### 1.下载
``` bash
cd /opt
wget https://www.redmine.org/releases/redmine-3.2.2.tar.gz --no-check-certificate

```

#### 2.解压
``` bash
tar -zxvf redmine-3.2.2.tar.gz
ln -s redmine-3.2.2 redmine

```
<!-- more -->
#### 3.配置与安装
``` bash
cd redmine/config
yum install ImageMagick-devel ImageMagick
bundle install
cp database.yml.example database.yml
vim database.yml
production:
  adapter: mysql2
  database: redmine
  host: localhost
  username: root
  password: 123456
  encoding: utf8
  pool: 5
```

#### 4.启动redmine
``` bash
rails s -e production -b 0.0.0.0
```

#### 5.补充
* 解决单元测试问题
``` bash
cd lib/tasks
mv testing.rake testing.rakebak20160614
rake db:migrate RAILS_ENV=production

```

* 解决没有认证密钥问题
``` bash
rake secret RAILS_ENV=production
echo "export SECRET_KEY_BASE=49afb388255c7b85b34f3c729d93b9bb4fdb059c47c6e64a2ea93f4202e60c6f481021dba51d7a1220b3dd82c110d5921295914d28050163dbb6be3651318978">>~/.bash_profile
source ~/.bash_profile
vim config/secrets.yml
production:
  secret_key_base: <%= ENV["SECRET_KEY_BASE"] %>
```