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
  - logtous.com
toc: true
date: 2016-12-07 14:49:24
thumbnail: http://easyread.ph.126.net/JsQ-sb6ykhVhOxwLpNH4Sg==/6597140135402662092.jpg
banner: http://easyread.ph.126.net/JsQ-sb6ykhVhOxwLpNH4Sg==/6597140135402662092.jpg
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
实现了发送带附件邮件的功能.