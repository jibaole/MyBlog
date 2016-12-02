---
title: activeadmin page render的实现
tags:
  - rails
categories:
  - Web development
toc: false
date: 2016-07-18 11:09:55
description: 
feature:
---

This is one way to do it:

In app/admin/stats.rb:
``` ruby
ActiveAdmin.register_page 'Stats' do

  content do
    render 'index'
  end

  controller do
    def index
      @foo = 'bar'
    end
  end

end
```
<!-- more -->
And rename app/views/admin/stats/index.html.erb to app/views/admin/stats/_index.html.erb (Notice the _)

And it works fine.

From what I understand, if index.html.erb is present in views/admin/stats, the content block is not called. If index.html.erb is renamed into something else, then we go into the content block, then the layout rendering is called...

参考：http://stackoverflow.com/questions/18617019/is-is-possible-to-render-a-normal-template-html-erb-template-with-active-admin