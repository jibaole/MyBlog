---
title: how to implement a boostrap template into rails
tags:
  - rails
categories:
  - Web development
toc: true
date: 2016-08-18 15:29:10
description: 参考：http://stackoverflow.com/questions/23019454/how-to-implement-a-boostrap-template-into-rails
feature:
---

#### Install the Gem

(From the project documentation on GitHub)

Add the gem to your application's Gemfile:
```
gem 'adminlte-rails'
```
Then run bundle to install the Gem:
```
$ bundle
```
Alternatively, you can install it with the gem command:
```
$ gem install adminlte-rails
```
<!-- more -->
#### Integrate Into Your Project

Finally, in order to integrate the theme with the other stylesheets of your project, add the following to your app/assets/javascripts/application.js:
```
//= require bootstrap.min
//= require admin-lte
```
And add the following to your app/assets/stylesheets/application.css:
```
*= require bootstrap
*= require font-awesome
*= require ionicons
*= require admin-lte
```
After that, you should be able to style your Project using all of the classes installed via the AdminLTE template. Let me know if you run into any other snags, I'll be working with the theme extensively while putting together a dashboard app as a front-end for a larger project.