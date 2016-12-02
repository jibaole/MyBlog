---
title: no implicit conversion of fixnum into string的解决办法
tags:
  - rails
categories:
  - Web development
toc: false
date: 2016-04-18 10:38:47
description: 参考：http://stackoverflow.com/questions/32914294/ruby-error-no-implicit-conversion-of-fixnum-into-string
feature:
---

``` ruby
puts("Your age, #{age} is a good one")
```