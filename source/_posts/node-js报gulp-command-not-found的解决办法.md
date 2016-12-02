---
title: node.js报gulp command not found的解决办法
tags:
  - nodejs
categories:
  - Web development
toc: true
date: 2015-11-21 10:08:07
description: 
feature:
---


Check that you installed gulp globally:
``` bash
npm install -g gulp
```
Another idea is to add scripts to your package.json file along the lines of:
``` json
{
  "name": "testing-app",
  "version": "0.9",
  "scripts": {
    "gulp": "gulp",
    "serve": "gulp serve"
  }
}
```
Then use npm run serve...

转自：http://stackoverflow.com/questions/30088550/gulp-command-not-found-mac-os/30088755