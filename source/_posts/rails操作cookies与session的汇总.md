---
title: rails操作cookies与session的汇总
tags:
  - rails
categories:
  - Web development
toc: true
date: 2016-08-18 15:44:12
keywords:
  - rails
  - cookies
  - session
description:
  - rails
  - cookies
  - session
feature:
---

>与 Java 和c#不同，Rails 操作 session 和 cookies 非常简单，可以在 controller 与 view 中直接访问，model 除外，极大地方便了开发 

### Session 
存信息
``` ruby
session[:current_user_id] = user.id
```
取信息
``` ruby
session[:current_user_id]
```
删除信息
``` ruby
session[:current_user_id] = nil
```
清空信息
``` ruby
reset_session　
```
<!-- more -->
### Cookies 
cookies 操作稍微有点多，因为关系到明文/加密，过期时长 
#### 1、默认为关闭浏览器，自动过期；明文存储。 
存信息
```
cookies[:id] = "rubyer.me"
```
取信息
```
cookies[:id] #输出"rubyer.me"
```
删除信息
```
cookies.delete (:key) #与 session 不同
```
清空信息（不能直接删除，置过期即可）
```
<% cookies[:id] = {:expires => 2.weeks.ago.utc} %>
```

#### 2、指定2周后过期 
```
cookies[:user_preference] = {  :value => @current_user.preferences,  :expires => 2.weeks.from_now.utc}
```

#### 3、永久存储。实际为20年后过期，非永久 
```
cookies.permanent[:user_preference] = @current_user.preferences
```

#### 4、加密存储，key 即为 config/initializers/secret_token.rb 中的 Application.config.secret_token 
```
cookies.signed[:id] = "45"
#存储结果类似cookies[:id]
#返回“BAhJIgc0NQY6BkVU--a07249e5ce4374f62b7af628c70c679caa11c10b”
#读取值cookies.signed[:id] #返回"45"
```

#### 5、加密并永久存储 
```
cookies.permanent.signed[:id] = "45"cookies.signed[:id]
```