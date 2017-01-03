---
title: rails环境变量的多种管理方法
tags:
  - rails
categories:
  - Web development
toc: true
date: 2016-05-15 15:52:02
keywords:
  - rails
  - 环境变量
description:
  - rails
  - 环境变量
feature:
---

#### 环境变量举例
``` yml
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

#### 方法一：添加到系统环境变量
``` bash
export GMAIL_USERNAME="myname@gmail.com"
```
<!-- more -->
#### 方法二：使用Figaro gem
1. 添加`gem 'figaro'`到文件Gemfile
2. 安装
``` bash
$ bundle install
$ bundle exec figaro install
```
3. 在`config/application.yml`中设置环境变量
``` 
GMAIL_USERNAME: Your_Username
```
4. 使用环境变量：Figaro.env.gmail_username

#### 方法三：使用一个local_env.yml文件
local_env.yml：
``` yml
# Rename this file to local_env.yml
# Add account settings and API keys here.
# This file should be listed in .gitignore to keep your settings secret!
# Each entry gets set as a local environment variable.
# This file overrides ENV variables in the Unix shell.
# For example, setting:
# GMAIL_USERNAME: 'Your_Gmail_Username'
# makes 'Your_Gmail_Username' available as ENV["GMAIL_USERNAME"]
GMAIL_USERNAME: 'Your_Gmail_Username'
```

config/application.rb：
``` ruby
# Version of your assets, change this if you want to expire all your assets
    config.assets.version = '1.0'
config.before_configuration do
  env_file = File.join(Rails.root, 'config', 'local_env.yml')
  YAML.load(File.open(env_file)).each do |key, value|
    ENV[key.to_s] = value
  end if File.exists?(env_file)
end
```
