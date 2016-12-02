---
title: bcloud获取bdstoken失败和network error的解决办法
tags:
  - ubuntu
categories:
  - Operating system
toc: true
date: 2015-11-15 16:57:54
description: 
feature:
---

#### 问题一:登录失败，提示获取bdstoken失败
bcloud是百度网盘的linux客户端，最近发现更新到最新版本后出现了一点小问题，登录失败，提示获取bdstoken失败，解决方法如下：
查找auth.py这个文件，一般在这个位置：
```
/usr/lib/python3/dist-packages/bcloud/auth.py
```
打开找到：
```
bds_re = re.compile('BDSTOKEN\s=\s"([^"]+)"')
```
替换为：
```
bds_re = re.compile('"bdstoken"\s*:\s*"([^"]+)"', re.IGNORECASE)
```
#### 问题二：Network error
bcloud向API请求数据时漏了STOKEN，SCRC这两条Cookie.
还有bcloud在抓取Cookie时没有抓取SCRC这条Cookie.
现在测试成功的临时解决方案：
1.在auth.py的get_bdstoken函数的if req:前一行添加一句`cookie.load_list(req.headers.get_all('Set-Cookie'))`,把SCRC加进cookie里去.
2.把pcs.py的所有函数中的cookie.sub_output()的参数添加上'SCRC','STOKEN'，或者干脆把cookie.sub_output(xxx)都换成cookie.header_output().
这个临时解决方案很粗暴简单。。。希望作者尽快修复bug。
还有logid好像没有什么影响的说。。。


#### 补充说明
经验证，以上两种方法都可以使用，如果使用上面方法还是出现“网络错误或Network error”请在终端输入以下命令：
```
rm -rf ~/.config/bcloud/*
rm -rf ~/.cache/bcloud/*
```
重新登录就好啦！


转自：http://www.cniee.com/linux/bcloud-bdstoken.html
