---
title: 在本地部署parse server
tags:
  - parse
categories:
  - Big Data technology
toc: true
date: 2016-10-21 11:25:11
description: 
  - parse server
  - 部署
keywords:
  - parse server
  - 部署
feature:
---

之前项目组一直在用Parse来做游戏的数据存储，后来听说Parse要关闭了，虽然觉得可惜，但当时也挺忙，没上心。最近在用Baasbox的时候发现一堆坑，这玩意又不流行，搞了大半年了，正式版都还没出，前景堪忧。于是寻思着用基于MongoDB的Baas，毕竟NoSQL里排行老大，用的人多，资料也很丰富。

昨天突然发现Parse的Server也开源了，Parse是我用过的Baas里感觉最顺手的，各个平台的接口写的堪称完美，我知道国内有个LeanCloud就是模仿的Parse。而且Parse就是基于MongoDB的，它的服务器和MongoDB是分开的，就是说MongoDB更新的话不会影响Parse。不像Baasbox是深度集成了OrientDB，想单独升级OrientDB的版本都不行。
<!-- more -->
关于Parse关闭的原因分析，可以参考知乎：http://www.zhihu.com/question/39948568，我觉得分析的还是比较靠谱的。

最后赶紧琢磨着在本地也部署一个。本人是win10 64位环境。

#### 准备工作
安装git和nodejs。

#### 部署MongoDB
1. 去官网下载一个社区版的就行。
2. 安装后，打开cmd，cd到目录C:\Program Files\MongoDB\Server\3.2\bin，然后执行以下命令启动MongoDB server。–dbpath就是你的数据库路径，设置为你想放置的路径即可，别忘了先创建这个db的路径。
```
C:\Program Files\MongoDB\Server\3.2\bin>mongod.exe --dbpath E:\MongoDB\db
```
3. MongoDB部署完毕！
#### 部署Parse Server
1. 去github上下载最新的Parse Server。可能有些人不熟github，不知道怎么下载，你看右边有个[Download ZIP]的按钮，点击就直接下载了。
2. 解压缩后得到一个parse-server-master的目录，打开cmd，cd到这个目录，执行以下命令。
等命令跑完后你会发现多了个node_modules的文件夹。命令执行期间如果出现找不到python的错误，不用理会。
```
E:\parse-server-master>npm install -g parse-server mongodb-runner
```
3. 在cmd里继续执行命令来启动Parse Server。appId和masterKey你可以随便给，最好复杂点。
```
parse-server --appId 123 --masterKey 123
```
4. Parse Server部署完毕！服务器地址为：http://localhost:1337/parse。
#### 部署Parse Dashboard
1. 从github上下载Parse Dashboard。
2. 解压后得到parse-dashboard-master目录，打开cmd，cd到这个目录，执行以下命令。
```
E:\parse-dashboard-master>npm install -g parse-dashboard
```
3. 继续执行以下命令。这里的appId和masterKey就是你前面设置的值。
```
parse-dashboard --appId 123 --masterKey 123 --serverURL "http://localhost:1337/parse" --appName YOUR_DB_NAME
```
4. Parse Dashboard部署完毕！服务器地址为：http://localhost:4040。此时你在浏览器里输入这个地址就能看到新版的Parse Dashboard了。
![](http://blog.shirlman.com/wp-content/uploads/2016/05/LocalParseDashboard.png)

#### Parse Dashboard配置文件
配置文件里允许配置多个app和用户，具体使用如下。

1. 打开配置文件E:\parse-dashboard-master\Parse-Dashboard\parse-dashboard-config.json，没有的话你在E:\parse-dashboard-master路径下新建一个parse-dashboard-config.json配置文件。
2. 编辑Json格式的配置文件并保存，内容如下。
``` json
{
  "apps": [
    {
      "serverURL": "http://localhost:1337/parse",
      "appId": "123",
      "masterKey": "123",
      "appName": "My Parse Server App"
    },
    {
      "serverURL": "http://api.parse.com/1",
      "appId": "myAppId",
      "masterKey": "myMasterKey",
      "appName": "My Parse Server App"
    }
  ],
"users": [
    {
      "user":"admin",
      "pass":"admin"
    },
    {
      "user":"myUserName",
      "pass":"myPassword"
    }
  ]
}
```
3. 开打cmd，cd到配置文件所在目录，执行以下命令
```
E:\parse-dashboard-master\Parse-Dashboard>parse-dashboard --conifg parse-dashboard-config.json --allowInsecureHTTP
```
这样就能用admin来登入Parse Dashboard，并管理多个app了！
#### 用HTTP访问Parse Dashboard
如果你是在远程服务器上部署了Parse，那么你在访问Parse Dashboard的时候会发现出现这个问题

>Parse Dashboard can only be remotely accessed via HTTPS

解决方案：在启动命令行后面加上–allowInsecureHTTP
```

parse-dashboard --appId 123 --masterKey 123 --serverURL "http://localhost:1337/parse" --appName YOUR_DB_NAME --allowInsecureHTTP
```
#### 写在最后
Parse Server和Parse Dashboard里面还有好多可以配置的东西，具体看需求去研究github对应页面（就是上面给出的下载页面）下的文档，写的很详细。

本地版本的Parse取消了一些附加的功能，觉得很可惜。比如Analytics，和数据导出功能（不过MongoDB都在本地了，那用一些工具直接从MongoDB导出应该更方便）。

![](http://blog.shirlman.com/wp-content/uploads/2016/05/oldParse.jpg)

今天又稍稍看了下Parse的文档，其实为什么不想用Baasbox，也是因为最近项目在查询的时候发现Baasbox很吃CPU，感觉对查询的优化很差，公司用的是亚马逊4核16G的机器，app里有个轮询，每6s查询一次数据，像这样where username = ‘xxx’，当查询这张表的时候，由于没做索引，导致每次查询都要查整张表，性能巨差，在同时100多人在线的情况下，CPU就长期处于100%了。这里先不讨论查询优化的问题（当然后面有做优化处理），这充分暴露了大部分Baas服务在这方面的不足，用户没法给单张表的单个字段专门做索引。于是特地看了看Parse是怎么解决这个问题的，洋洋洒洒的写了一大堆，首先Parse似乎也没法让用户自己建索引，但是它有考虑这个问题，于是有个智能索引的玩意，如果开发人员掌握了一些查询的小技巧的话，那么就可以利用到它的智能索引系统从而大大提高查询的效率。具体可以参阅https://www.parse.com/docs/android/guide#performance，我也没完全看完。当然还有一些特殊的字段来帮你优化存储结构，比如Relation和Pointer。

总之Parse的后端数据服务体验加上主流的MongoDB，我觉得自己来弄的话已经是完美的选择了，如果觉得第三方数据存储服务不靠谱的话，那就自己架个服务器来部署吧。毕竟数据放在别人那不放心，像Parse这样优秀的Baas服务商都会关闭，指不定哪天第三方的数据服务就关闭了，后面还有一堆杂事要处理就坑了。

另外还有以下问题还需要去了解的：

* 集群。首先肯定是能建立集群的，这个应该是在MongoDB那搞，我知道MongoDB自身就能很方便的建立集群，然后Parse Server连接到集群后的MongoDB，以后一台服务器搞不定了还能扩张。
* Could code能否debug。这个我很想知道，以前在Parse上写cloud code的时候只能通过打log来分析问题，现在可以本地部署了server了，那么应该有方法直接在本地debug。
* Parse官方的Job服务在本地版本上取消了，那么怎么在Parse上运行一些本地执行的任务呢。

转自：http://www.shirlman.com/tec/20160507/367
