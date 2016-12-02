---
title: 制作cocoapods国内镜像
tags:
  - ios
categories:
  - Mobile development
toc: true
date: 2016-10-21 13:54:57
description: 转自：http://lloydsheng.com/post/setup-cocoapods-mirror
feature:
---

国内访问cocoapods spec repo速度非常慢，网络不好的话做一次pod update要等半个小时以上。

#### 初始化
``` bash
git clone --mirror https://github.com/CocoaPods/Specs.git
```

#### 编辑config文件
```
[core]
        repositoryformatversion = 0
        filemode = true
        bare = true
[remote "origin"]
        fetch = +refs/heads/*:refs/heads/*
        fetch = +refs/tags/*:refs/tags/*
        mirror = true
        url = https://github.com/CocoaPods/Specs.git
[remote "mirrors"]
        url = git@gitcafe.com:lloydsheng/Specs.git
        mirror = true
        skipDefaultUpdate = true
```
<!-- more -->
#### 定期同步
* 编辑同步脚本specssync.sh，添加：
``` bash
git fetch remote
git push mirrors
```
* 设置cronjob每半个小时同步一次
```
30 * * * * /home/git/specssync.sh  > /var/log/specssync.log 2>&1
```

#### 使用镜像
``` bash
pod repo remove master
pod repo add master git@gitcafe.com:lloydsheng/Specs.git
```

最后，如果你不想花时间自己搭的话，可以使用我在gitcafe上搭建好了的镜像。
https://gitcafe.com/lloydsheng/Specs