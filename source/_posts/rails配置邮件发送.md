---
title: rails配置邮件发送
tags:
  - rails
categories:
  - Web development
toc: true
date: 2016-06-15 16:09:19
description:
feature:
---

#### 配置ActionMailer
config/environments/development.rb：
``` ruby
# ActionMailer Config
config.action_mailer.default_url_options = { :host => 'localhost:3000' }
config.action_mailer.delivery_method = :smtp
# change to true to allow email to be sent during development
config.action_mailer.perform_deliveries = false
config.action_mailer.raise_delivery_errors = true
config.action_mailer.default :charset => "utf-8"
```
<!-- more -->
#### 使用一个Gmail邮箱账号
``` ruby
config.action_mailer.smtp_settings = {
  address: "smtp.gmail.com",
  port: 587,
  domain: "example.com",
  authentication: "plain",
  enable_starttls_auto: true,
  user_name: ENV["GMAIL_USERNAME"],
  password: ENV["GMAIL_PASSWORD"]
}
```