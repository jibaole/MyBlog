---
title: rails link_to data-toggle
tags:
  - rails
categories:
  - Web development
toc: false
date: 2016-08-18 15:23:47
description: 
feature:
---
```
<%= link_to 'my site', 'mysite.example.com', { :class => 'tooltips', 'data-toggle' => 'tooltip', 'data-placement' => 'top', :title => '', 'data-original-title' => 'Facebook' } %>
```
Or
```
<%= link_to 'my site', root_path, class: 'tooltips', title: '', data: { toggle: 'tooltip', placement: 'top', original_title: 'Facebook' } %>
```
Or
```
<%= link_to 'my site', { controller: 'home', action: 'index' }, { class: 'tooltips', title: '', data: { toggle: 'tooltip', placement: 'top', original_title: 'Facebook' } } %>
```

参考：http://stackoverflow.com/questions/29435959/rails-4-data-toggle-link-to