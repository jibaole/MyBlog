---
title: ruby gem mail的简单使用
tags:
  - ruby
categories:
  - Scripting language
keywords:
  - ruby
  - mail
  - gem
description:
  - ruby
  - mail
  - gem
toc: true
date: 2016-12-07 14:49:24
thumbnail: http://7xtlfa.com1.z0.glb.clouddn.com/ruby.jpg
banner: http://7xtlfa.com1.z0.glb.clouddn.com/ruby.jpg
---

#### 安装mail gem
``` bash
gem install mail
```

#### 编写mail脚本
``` ruby
#!/usr/bin/ruby
require 'mail'

smtp = { :address => 'smtp.163.com', :port => 25, :domain => '163.com', \
 :user_name => 'xxx@163.com', :password => 'xxx',\
 :enable_starttls_auto => true, :openssl_verify_mode => 'none' }
Mail.defaults { delivery_method :smtp, smtp }
mail = Mail.new do
  from 'xxx@163.com'
  to 'xxx@qq.com'
  subject 'xxx'
  add_file File.expand_path("/opt/xxx.log")
end
mail.deliver!
```
<!-- more -->
实现了发送带附件邮件的功能.