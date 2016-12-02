---
title: centos上架设svnserver
tags:
  - svn
categories:
  - Version control
toc: true
date: 2015-11-14 10:56:05
description: 转自：http://write.blog.csdn.net/postedit/22666981
feature:
---

### 前言
公司研发部搬家，需要新搭建一个SVN服务器，经过了一天的研究终于顺利搞定。SVN服务安装和配置其实都很简单，分分钟就搞定了，研究的时间主要花费在想找到一个通过浏览器管理和配置SVN服务（添加/删除 repo、添加/删除 用户、分配用户组和权限等 ）。
所以对比和尝试安装了几个开源的项目，下面是安装和简单使用各个开源项目的部分心得：

* [submin2](http://supermind.nl/submin/)
python写的，安装和配置都很简单，但是最郁闷的是这个玩意第一次登陆的时候需要发邮件，最后各种配置stmpServer最后也没发成功。问题和https://ssl.supermind.nl/collab/projects/submin/ticket/335 描述的一样，下面回复提出的解决方案，全部试过，一点问题没有但是最后还是发不出去邮件，本人又不会python，所以… 果断放弃了。
<!-- more -->
* [submin](http://supermind.nl/submin/)
上面的那个的1.0版本，有人说有2.0版本为啥还试老的版本呢……原因只有一个：”不用发邮件！”，但是1.0版本有个问题，1.0版本是基于python1.x开发的，我使用的操作系统默认装的是python2.x版本。所以python1里面的很多方法在python2里面都不支持了，而且还不主持chrome，结果一脸黑。用python2跑submin1的时候，我修改了import md5的方式后，大部分功能是可以使用的，但是有一些小功能就和你拜拜了，作为一个有点完美主义倾向的程序猿来说这个是不能接受地。所以…果断放弃了。
* [svn manager](http://svnmanager.sourceforge.net/)
php写的。用mod_dav_svn、mod_authz_svn和pear的VersionControl_SVN管理SVN服务。身份一枚PHP攻城狮的我来说，这种还是比较熟悉一点，安装主要是改配置文件，比较简单，没什么可说的。虽然这个玩意也需要发邮件，但是我配置了stmpSever以后邮件顺利的发出来了，心情一下愉悦起来。不过需要主义的是我用的版本是（v1.10）修改权限信息的时候代码里面有个小bug – -！具体是那个文件忘了，直接看apache的错误日志就能看到。修改后一切安好，但是在设置repo的权限那块貌似代码写的不好，设置成功以后不起作用，而且界面实在是…有点…那个，对于一个完美主义的…所以果断放弃了。
* [usvn](http://www.usvn.info/)
也是php写的。但是跟着官网给的installation安装失败了，一堆错。虽然在官网给的installation下面就有个commons problems。结果照了半天也没找到我的错误。所以…不说了
* [svn access manager](http://www.svn-access-manager.org/)
还是php。看了一眼官网的installation。直接放弃了…
* [svnpod](https://github.com/layerssss/svnpod)
这个是用js写的，用的nodejs。界面看着感觉比较好。但是我感觉功能少了点，貌似只有一个用户管理就没试。不过推荐一下。希望可以他顺着现在的风格能把功能继续完善。而且用nodejs还是比较新颖的。
* [iF.SVNAdmin](http://svnadmin.insanefactory.com/)
试了一圈以后最后找到了这个svnadmin。也是使用php写的。他吸引我的主要是安装和配置都非常简单和人性化。而且也有强大的功能，。支持repo管理、用户管理、用户组管理、权限管理、LDAP等，界面可以算是中规中矩。所以最后我选择了iF.SVNAdmin。
啰嗦了那么半天主要还是想对现在正在寻找这类项目的同学有点帮助，下面进入正题。


### 介绍
* [Apache Subversion](http://subversion.apache.org/)
不用说太多，Apache基金会下的一个开源项目，很多企业用subversion作为项目代码的版本管理。虽然不像git那样可以真正的离线使用（主要还是架构设计的问题），但是贵在免费，哦不，是贵在开源，所以现在还是很多人都在用的。
* [iF.SVNAdmin](http://svnadmin.insanefactory.com/)
一个基于WEB GUI的方式管理SVN，包括：repos管理、用户管理、用户组管理、权限管理等多种功能，底层也很简单，是通过修改`Subversion authorization` 和 `user authorization`两个文件完成其管理功能（现不支持数据库user authorization的那种），整体使用下来给我的感觉就是两个字简单，从安装到配置到使用到架构，都很简单。


### 准备
>操作系统：CentOS-6.5-64bit
版本管理：Subversion-1.6.11
管理软件：iF.SVNAdmin-1.6.2-stable


#### 1. 安装Apache
```
[root@developbase ~]# yum install httpd

```

#### 2. 安装SVN
```
[root@developbase ~]# yum install mod_dav_svn subversion
```

#### 3. 配置SVN
```
[root@developbase ~]# vim /etc/httpd/conf.d/subversion.conf
<Location /svn>
  DAV svn
  SVNParentPath /var/www/svn
  AuthType Basic
  AuthName “Restricted Access”
  #File will be created by SVNManager, keep location in mind!
  AuthUserFile /var/www/svnconfig/passwdfile
  Require valid-user
  #SSLRequireSSL
  #File will be created by SVNManager, keep location in mind
  AuthzSVNAccessFile /var/www/svnconfig/accessfile
  # Limit write permission to list of valid users.
  #<LimitExcept GET PROPFIND OPTIONS REPORT>
  #   # Require SSL connection for password protection.
  #   # SSLRequireSSL
  #   AuthType Basic
  #   AuthName “Authorization Realm”
  #   AuthUserFile /path/to/passwdfile
  #   Require valid-user
  #</LimitExcept>
</Location>
```


#### 4. 创建存放repo目录和权限信息目录
```
[root@developbase ~]# mkdir -p /var/www/svn /var/www/svnconfig

```

#### 5. 创建SVN权限信息文件
```
[root@developbase ~]# touch /var/www/svnconfig/accessfile /var/www/svnconfig/passwdfile
#/var/www/svnconfig/accessfile：控制用户&用户组读写（r/w）权限使用
#/var/www/svnconfig/passwdfile：控制用户信息
```

#### 6. 安装php
```
[root@developbase conf.d]# yum install php

```


#### 7. 下载SVNAdmin
http://sourceforge.net/projects/ifsvnadmin/files/svnadmin-1.6.2.zip/download


#### 8. 解压SVNAdmin
```
[root@developbase ~]# unzip svnadmin-1.6.2.zip
```

#### 9. 拷贝项目到WebServer目录中
```
[root@developbase ~]# cp -r iF.SVNAdmin-stable-1.6.2/ /var/www/html/svnadmin

```

#### 10.赋权
```
[root@developbase ~]# chmod -R 777 /var/www/html/svnadmin/data/
[root@developbase ~]# chown -R apache:apache /var/www/html/svnadmin/

```

#### 11.启动WebServer服务
```
[root@developbase ~]# service httpd restart
```
启动webserver服务后，浏览器地址输入http://servername/svnadmin 出现配置界面，输入下图中配置信息，输入每个配置信息可以点击旁边的Test测试是否输入正确
输入完成后点击`Save configuration`。提示默认账户admin/admin
使用默认账户admin登录后，可以查看版本信息等。
点击`Repositories`并选择Add，创建一个repo
输入需要创建的repo名称，其他默认即可，然后点击Create，创建完成repo后，点击Access-Paths添加权限
赋予admin 读写权限
通过http://servername/svn/reponame ，使用admin账户进入刚刚建立完成的repo
之后就可以通过svn客户端checkout或者import代码进行版本管理了。谢谢大家
