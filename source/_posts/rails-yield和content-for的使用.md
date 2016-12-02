---
title: rails yield和content_for的使用
tags:
  - rails
categories:
  - Web development
toc: true
date: 2016-09-18 15:17:22
description: 参考：http://stackoverflow.com/questions/13995927/add-class-to-body-using-erb-in-a-view-rails
feature:
---


In a layout file:
``` erb
<body class="<%= yield (:body_class) %>">
```

In a view:
``` ruby
<% content_for :body_class, "my_class" %>
```



<!-- more -->
Sometimes using the current controller name as a class name we'll do:
``` erb
<body class="<%= controller.controller_name %>">
```
I find this simpler and a bit more elegant, but of course thus you won't be able to assign individual class names.


In the layout page:
```
<% if content_for?(:body_class) %>
  <body class="<%= content_for(:body_class) %>" >
<% else %>
 <body>
<% end %>
```
In the content page:
```
<% content_for :body_class do  'my-body-class' end %>
```