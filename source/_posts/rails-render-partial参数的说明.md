---
title: rails render partial参数的说明
tags:
  - rails
categories:
  - Web development
toc: true
date: 2016-09-18 11:20:03
description: 转自：http://hlee.iteye.com/blog/1084702
feature:
---

这里的conventions总是容易记不清楚 就是locals collection object 


#### 1. 默认参数 
``` ruby
<%= render :partial => "account" %>
```

默认本地有个变量@account, 传递过去，render到的partial（_account.erb）有个变量account 

#### 2. 单独参数 locals 
locals传递一组hash参数hash 值是本地的变量，hash的key是partial里的变量 
``` ruby
<%= render :partial => "account", :locals => { :account => @buyer } %>  
  
<% for ad in @advertisements %>  
  <%= render :partial => "ad", :locals => { :ad => ad } %>  
<% end %>   
```
上面两个render分别 
传递本地变量@buyer到_account.erb里叫account的参数 
传递本地变量ad到_ad.erb里叫ad 
<!-- more -->
#### 3.根据1默认参数下面两个是一样的 
``` ruby
<%= render :partial => "contract", :locals => { :contract  => @contract } %>  
  
<%= render :partial => "contract" %>  
```


#### 4. as使用 

用来改变传递之后，partial里变量的名称，如下，render方式是一样的。 
``` ruby
<%= render :partial => "contract", :as => :agreement  
  
<%= render :partial => "contract", :locals => { :agreement => @contract }  
```
传递@contract到_contract.erb里，partial里的变量名为agreement 

#### 5. object使用 

object最简单，就是把一个变量原名传递到partial里， 
所以什么都记不清楚的时候，就用object多写点都能表达 
``` ruby
<%= render :partial => "account", :object => @buyer %>  
  
<% for ad in @advertisements %>  
  <%= render :partial => "ad", :object => ad %>  
<% end %>  
```
传递@buyer到_account.erb的partial里变量名还是@buyer 
传递ad到_ad.erb的partial里变量名还是ad 

#### 6.object和as合用 
``` ruby
<%= render :partial => "contract", :object => @contract, :as => :contract %>  
  
<%= render :partial => "contract" %>  
```

#### 7.collection 
``` ruby
<%= render :partial => "ad", :collection => @advertisements %>  
```

@advertisements是一个array，partial里_ad.erb有个ad变量是@advertisements的成员。就是_ad.erb不用写loop只是一条广告显示。 
``` ruby
<%= render :partial => "ad", :collection => @advertisements, :spacer_template => "ad_divider" %>  
```
同上，循环显示_ad.erb _ad_divider.erb 显示@advertisements.size次其中partial_ad_counter是默认计数器表示第几条广告 

#### 8.默认 

看你要partial的变量是一组记录还是一条记录，会对应约定用locals和collection 
``` ruby
#@account是一条记录  
# <%= render :partial => "accounts/account", :locals => { :account => @account} %>  
<%= render :partial => @account %>  
  
# @posts是一组记录  
# <%= render :partial => "posts/post", :collection => @posts %>  
<%= render :partial => @posts %>  
```
这是很人性的约定，可有时候就是忘了 

#### 9.一些漂亮的简写 
``` ruby
#<%= render :partial => "account" %>可用下面代替  
<%= render "account" %>  
  
#<%= render :partial => "account", :locals => { :account => @buyer } %>可用下面代替  
<%= render "account", :account => @buyer %>  
  
# @account是一条记录  
# <%= render :partial => "accounts/account", :locals => { :account => @account } %>可用下面代替  
<%= render(@account) %>  
  
# @posts是一组记录  
# <%= render :partial => "posts/post", :collection => @posts %>可用下面代替  
<%= render(@posts) %>  
```

#### 10.layout 
```
<%# app/views/users/index.html.erb &>  
Here's the administrator:  
<%= render :partial => "user", :layout => "administrator", :locals => { :user => administrator } %>  
  
Here's the editor:  
<%= render :partial => "user", :layout => "editor", :locals => { :user => editor } %>  
  
<%# app/views/users/_user.html.erb &>  
Name: <%= user.name %>  
  
<%# app/views/users/_administrator.html.erb &>  
<div id="administrator">  
  Budget: $<%= user.budget %>  
  <%= yield %>  
</div>  
  
<%# app/views/users/_editor.html.erb &>  
<div id="editor">  
  Deadline: <%= user.deadline %>  
  <%= yield %>  
</div>  
```