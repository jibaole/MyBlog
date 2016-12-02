---
title: rails activeadmin
tags:
  - rails
categories:
  - Web development
toc: true
date: 2016-07-18 10:33:25
description:
feature:
---

#### 一、开始ActiveAdmin
Active Admin是一个发布在RAILS3中使用的Gem。
1、我们为了快速开始我们对Active Admin的了解，我们首先安装它：
在你GemFile中添加
```  ruby
gem 'activeadmin'
```
2、更新你的Gem
``` bash
bundle install
```
3、运行installer
``` bash
rails generate active_admin:install
```
<!-- more -->
4、安装的installer会创建一个initializer，这个initializer采用Active Admin默认的配置，把所有需要的配置都写进一个文件夹app/admin里面。
同时，这个时候会显示相关的配置信息，并按照配置信息完成相关指示操作。
```
Some setup you must do manually if you haven't yet:

  1. Ensure you have defined default url options in your environments files. Here
     is an example of default_url_options appropriate for a development environment
     in config/environments/development.rb:

       config.action_mailer.default_url_options = { :host => 'localhost:3000' }

     In production, :host should be set to the actual host of your application.

  2. Ensure you have defined root_url to *something* in your config/routes.rb.
     For example:

       root :to => "home#index"

  3. Ensure you have flash messages in app/views/layouts/application.html.erb.
     For example:

       <p class="notice"><%= notice %></p>
       <p class="alert"><%= alert %></p>

  4. If you are deploying Rails 3.1+ on Heroku, you may want to set:

       config.assets.initialize_on_precompile = false

     On config/application.rb forcing your application to not access the DB
     or load models when precompiling your assets.

  5. You can copy Devise views (for customization) to your app by running:

       rails g devise:views
```
完成以上操作，我们还需要配置邮件服务器
编辑config/initializers/devise.rb
``` ruby
  # Configure the e-mail address which will be shown in DeviseMailer.
  config.mailer_sender ="xxx@126.com"   #换成你的邮箱，最好不要是gmail
```
编辑config/environments/development.rb
``` ruby
 # Don't care if the mailer can't send
  config.action_mailer.raise_delivery_errors =true  #此处改为true
  config.action_mailer.default_url_options = {:host => "localhost:3000" } #刚才devise的提示中提到这一句
  config.action_mailer.delivery_method =:smtp
  config.action_mailer.smtp_settings = {
    :address=> "smtp.126.com",
    :port=> 25,
    :domain=> "126.com",
    :authentication=> :login,
    :user_name=> "xxx@126.com",#你的邮箱
    :password=> "xxxxxx" #你的密码
  }
```
5、迁移你的db ，rake db:migrate，然后运行rails server 。
6、然后打开浏览器，输入 localhost:3000/admin 进行访问。
```
用户名： admin@example.com
密码：password
```
7、添加你的model
``` bash
rails g model  [MyModelName]  name:string title:string content:text
```
8、将model放到admin目录中去 
```
rails generate active_admin:resource [MyModelName]
```
这个命令会创建一个文件app/admin/my_model_names.rbl来配置你的resource。
刷新你的浏览器，看看这个时候的界面是怎么样的。
#### 二、ActiveAdmin更新
你可以键入如下命令进行对它的升级
```
rails generate active_admin:assets
```
如果提示：uninitialized constant Admin::DashboardController
这个时候，需要dashboard view（app/admin/dashboards.rb）和初始化的时候一样。
#### 三、下一步
1、最好得到的阅读文档的地方：activeadmin.info/documentation.html.
2、AcitiveAdmin 的示例网址：demo.activeadmin.info
3、如果有问题，在这个网站的邮箱列表（groups.google.com/group/activeadmin）中发邮件。
#### 四、补充说明
1、我在实际安装过程中，安装了很多的东西，这里我把安装过程中的gem一并写下来，就按gemfile里写的格式写下来。
``` ruby
gem 'activeadmin','0.5.1'
gem 'arbre','1.0.1'
gem 'bourbon','1.0.0'
gem 'devise','1.5.4'
gem 'formtastic','2.1.0'
gem 'has_scope','0.5.1'
gem 'inherited_resources','1.3.1'
gem 'meta_search','1.1.3'
gem 'orm_adapter','0.0.3'
gem 'polyamorous','0.5.0'
gem 'responders','0.6.5'
gem 'warden','1.1.1'
```
2、install过后，截图纪念一下
![](http://s1.sinaimg.cn/mw690/82544279td85c34e222b0&690)
![](http://s8.sinaimg.cn/mw690/82544279td85c38a362c7&690)
![](http://s11.sinaimg.cn/mw690/82544279td85c38cc63aa&690)

#### 五、完成安装过后，又开始我们的下一个旅程——订制自己的后台。
1、给menu，index页面，form页面还有filter和help添加一些内容和中文命名
``` ruby
ActiveAdmin.register Post  do 
  menu :label => "文章管理"
    index  do  
    column "标题",:title                  
    column "作者",:name      
    column "内容",:content                 
    default_actions    
  end        
  
  filter :title ,:label=>"标题"
  filter :name ,:label=>"作者"
  filter :content ,:label=>"内容"
  
  form do |f|
    f.inputs "添加标题和作者" do
      f.input :title,:label=>"标题"
      f.input :name,:label=>"作者"
    end
    f.inputs "添加内容" do
      f.input :content,:label=>"文章内容"
    end
    f.buttons
  end
  
  sidebar :help,:only => :index do
  "如果你对网站后台管理有问题,请联系CreateByRails@yeah.net"
  end

end
```
#### 六、汉化页面

1、现在将整个页面汉化一下
首先，我们已经在config/application.rb里配置了local为 zh-CN， 
```
    # The default locale is :en and all translations from config/locales/*.rb,yml are auto loaded.
     config.i18n.load_path += Dir[Rails.root.join('config', 'locales', '*.{rb,yml}').to_s]
     config.i18n.default_locale = "zh-CN"
```
然后到http://github.com/tsechingho/rails-i18n/blob/master/rails/locale/zh-CN.yml
下载已经配置好的中文包到config/locales里，这样，rails的中文化已经做好了。
2、devise的汉化
到该Wiki去下载汉化包 devise.zh-CN.yml
https://github.com/plataformatec/devise/wiki/I18n
然后删除原来的devise.en.yml
3、解决编码问题
在你需要显示汉语的*.rb文件中 ，添加一句代码
``` ruby
#encoding:utf-8
```