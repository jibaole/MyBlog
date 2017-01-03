---
title: ruby版本管理器rvm的安装
tags:
  - ruby
categories:
  - Scripting language
toc: true
date: 2016-04-17 16:00:52
keywords:
  - ruby
  - rvm
description:
  - ruby
  - rvm
feature:
---

#### 1. 在线脚本安装
``` bash
curl https://raw.githubusercontent.com/rvm/rvm/master/binscripts/rvm-installer | bash -s stable
```
#### 2. 登出账户
``` bash
exit
```

#### 3. 添加rvm到环境变量中
``` bash
source /etc/profile.d/rvm.sh
```
<!-- more -->
#### 4. 查询可安装ruby版本
``` bash
$ rvm list known
# MRI Rubies
[ruby-]1.8.6[-p420]
[ruby-]1.8.7[-head] # security released on head
[ruby-]1.9.1[-p431]
[ruby-]1.9.2[-p330]
[ruby-]1.9.3[-p551]
[ruby-]2.0.0[-p648]
[ruby-]2.1[.8]
[ruby-]2.2[.4]
[ruby-]2.3[.0]
[ruby-]2.2-head
ruby-head
......
$ rvm install ruby-2.0.0-p648
$ /usr/local/rvm/src/ruby-2.0.0-p648/ruby -v #测试是否安装成功
```

#### 5. 将新安装的ruby添加到当前帐户的环境变量中
``` bash
vim $HOME/.bash_profile
PATH=/usr/local/rvm/src/ruby-2.0.0-p648:$PATH:$HOME/bin
export PATH
```
注意：默认系统自带ruby是在$PATH变量中，所以如果希望使用新版本ruby，需要将环境变量添加到最前面，这样就可以直接覆盖老版本.

#### 6. 使环境变量立即生效
``` bash
source $HOME/.bash_profile
```