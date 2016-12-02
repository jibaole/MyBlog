---
title: 彻底干净的卸载rails
tags:
  - rails
categories:
  - Web development
description: 
toc: true
date: 2016-06-08 17:26:11
feature:
---

#### 最终解决方案
```
$ which rails
~/software/bin/rails
$ rails -v
Rails 4.0.0
$ gem list | grep rails
rails (4.0.0, 3.2.13, 3.2.9, 3.2.6)
$ gem uninstall rails -v 4.0.0

You have requested to uninstall the gem:
        rails-4.0.0
figaro-0.7.0 depends on [rails (< 5, >= 3)]
.....
.....
Continue with Uninstall? [Yn]  y
Successfully uninstalled rails-4.0.0
$ gem list | grep rails
rails (3.2.13, 3.2.9, 3.2.6)
$ rails  -v
Rails 4.0.0
$ gem uninstall railties -v 4.0.0

You have requested to uninstall the gem:
        railties-4.0.0
coffee-rails-4.0.0 depends on [railties (< 5.0, >= 4.0.0.beta)]
....
....
If you remove this gems, one or more dependencies will not be met.
Continue with Uninstall? [Yn]  y
Successfully uninstalled railties-4.0.0
$ rails -v
Rails 3.2.13
```
<!-- more  -->
#### 为什么要这样卸载?
```
$ tail `which rails`
  str = ARGV.first
  str = str.dup.force_encoding("BINARY") if str.respond_to? :force_encoding
  if str =~ /\A_(.*)_\z/
    version = $1
    ARGV.shift
  end
end

gem 'railties', version
load Gem.bin_path('railties', 'rails', version)
```

参考：https://mccdn.qcloud.com/img56a609b19718b.png
 