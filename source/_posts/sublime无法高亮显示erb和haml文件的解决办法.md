---
title: sublime无法高亮显示erb和haml文件的解决办法
tags:
  - sublime
categories:
  - Development tools
toc: false
date: 2016-04-17 16:40:02
description: 
feature:
---
```
>>> sublime.find_resources("HTML.sublime-settings")
['Packages/One Dark Material - Theme/prefs/Ext/HTML.sublime-settings']
Which is a theme I wasn't using, but rather:

"theme": "Cobalt2.sublime-theme",
"color_scheme": "Packages/Theme - Cobalt2/cobalt2.tmTheme",
I removed the One Dark Material package and now it shows:

>>> sublime.find_resources("HTML.sublime-settings")
[]
```
..and voila! Now working as expected. Opening an html.erb file sets the syntax to HTML (Rails).

