---
title: 使fork的项目与原项目保持同步更新的方法
categories:
  - Version control
toc: false
date: 2015-11-08 14:46:19
tags:
  - git
description: 
  - git
  - fork
  - 同步
keywords:
  - git
  - fork
  - 同步
feature:
---

1. 在fork的代码库中添加原项目的remote源
如: 其中# upstream 表示上游代码库名， 可以任意。
```
git remote add upstream https://github.scm.corp.ebay.com/montage/frontend-ui-workspace
```

2. 将本地的修改提交commit

3. 在每次 Pull Request 前做如下操作：
```
git remote update upstream
git rebase upstream/{branch name}

注意：在操作3.2之前，一定要将checkout到{branch name}所指定的branch
```

4. push代码到github
```
git push
```

参考：http://www.tuicool.com/articles/Mnmmqyi