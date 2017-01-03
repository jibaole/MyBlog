---
title: 为gitlab配置网易163邮箱
tags:
  - git
categories:
  - Version control
toc: true
date: 2016-09-11 11:21:15
description: 
  - gitlab
  - 网易
  - 163邮箱
keywords:
  - gitlab
  - 网易
  - 163邮箱
feature:
---

#### 1. 配置SMTP发送邮件配置
```
$ sudo vi /etc/gitlab/gitlab.rb                            
#Sending application email via SMTP
gitlab_rails['smtp_enable'] = true
gitlab_rails['smtp_address'] = "smtp.163.com"
gitlab_rails['smtp_port'] = 25 
gitlab_rails['smtp_user_name'] = "xxx@163.com"
gitlab_rails['smtp_password'] = "xxx"
gitlab_rails['smtp_domain'] = "163.com"
gitlab_rails['smtp_authentication'] = "login"
gitlab_rails['smtp_enable_starttls_auto'] = true

```

#### 2. 注册邮箱帐号进行测试
```
$ sudo gitlab-ctl tail
2014-07-11_16:12:08.43945 2014-07-11T16:12:08Z 12595 TID-dxf7c Sidekiq::Extensions::DelayedMailer JID-061604dc558ce8560b273cbe 
INFO: fail: 0.574 sec2014-07-11_16:12:08.44955 2014-07-11T16:12:08Z 12595 TID-dxf7c WARN: {"retry"=>true, "queue"=>"default", 
"class"=>"Sidekiq::Extensions::DelayedMailer", "args"=>["---\n- !ruby/class 'Notify'\n- :project_access_granted_email\n- - 4\n"], 
"jid"=>"061604dc558ce8560b273cbe", "enqueued_at"=>1405094359.354158, "error_message"=>"553 Mail from must equal authorized user\n", 
"error_class"=>"Net::SMTPFatalError", "failed_at"=>"2014-07-11 15:59:28 UTC", "retry_count"=>5, "retried_at"=>2014-07-11 16:12:08 UTC}

```
报错原因：
网易服务器smtp机器要求身份验证帐号和发信帐号必须一致，如果用户在发送邮件时，身份验证帐号和发件人帐号是不同的，因此拒绝发送。

#### 3.解决报错方法
```
$ sudo vi /etc/gitlab/gitlab.rb                            
gitlab_rails['gitlab_email_from'] = "xxx@163.com"
user["git_user_email"] = "xxx@163.com"
```

