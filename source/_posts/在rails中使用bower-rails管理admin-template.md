---
title: 在rails中使用bower-rails管理admin template
tags:
  - rails
categories:
  - Web development
toc: false
date: 2016-05-15 15:11:39
description: 
  - rails
  - bower-rails
  - admin
keywords:
  - rails
  - bower-rails
  - admin
feature:
---

#### 实现步骤
1. Gemfile中加入`gem "bower-rails", "~> 0.9.2”`
2. 在项目根目录创建一个文件Bowerfile
``` 
asset "admin-lte", ‘2.1.1’
```
3. 使用bower安装adminlte template
``` bash
$ rake bower:install
$ echo /vendor/assets/bower_components >> .gitignore
```
  注：安装后的assets在：vendor/assets/bower_components/admin-lte/ 目录下
<!-- more -->
4. 确认是否正确安装
``` bash
$rails c
Running via Spring preloader in process 88378
Loading development environment (Rails 5.0.0.1)
2.3.0 :001 >pp Rails.application.config.assets.paths ;nil
["/Users/guoqing/Desktop/study-rails/app/assets/config",
 "/Users/guoqing/Desktop/study-rails/app/assets/images",
 "/Users/guoqing/Desktop/study-rails/app/assets/javascripts",
 "/Users/guoqing/Desktop/study-rails/app/assets/stylesheets",
 "/Users/guoqing/Desktop/study-rails/vendor/assets/javascripts",
 "/Users/guoqing/Desktop/study-rails/vendor/assets/stylesheets",
 "/Users/guoqing/Desktop/study-rails/vendor/assets/bower_components",
 "/Users/guoqing/.rvm/gems/ruby-2.3.0/gems/jquery-rails-4.2.1/vendor/assets/javascripts",
 "/Users/guoqing/.rvm/gems/ruby-2.3.0/gems/coffee-rails-4.2.1/lib/assets/javascripts",
 "/Users/guoqing/.rvm/gems/ruby-2.3.0/gems/actioncable-5.0.0.1/lib/assets/compiled",
 "/Users/guoqing/.rvm/gems/ruby-2.3.0/gems/turbolinks-source-5.0.0/lib/assets/javascripts"]
 => nil

```

5. 将application.css改为application.css.scss
``` bash
mv app/assets/stylesheets/application.css app/assets/stylesheets/application.css.scss
```

6. 编辑application.css.scss
``` scss
*= require admin-lte/bootstrap/css/bootstrap
*= require admin-lte/dist/css/AdminLTE
*= require admin-lte/dist/css/skins/skin-blue
*= require_self
*/
```

7. 编辑application.js
``` js
//= require jquery 
//= require jquery_ujs
//= require admin-lte/bootstrap/js/bootstrap
//= require admin-lte/dist/js/app.js 
//= require_tree .
```

8. Layout
* 使用AdminLTE中的starup.html做layout
* 覆盖layout/application.html.erb
  * main content 中添加`<%= yield %>`
* 图片处理
  * 添加 `Rails.application.config.assets.precompile += %w( admin-lte/dist/img/user2-160x160.jpg ) to config/initializers/assets.rb`
  * 修改页面中的图片引用：`<img src="<%= image_path('admin-lte/dist/img/user2-160x160.jpg') %>" class="img-circle" alt="User Image">`