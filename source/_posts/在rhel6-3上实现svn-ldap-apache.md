---
title: 在rhel6.3上实现svn+ldap+apache
tags:
  - svn
categories:
  - Version control
toc: true
date: 2015-10-14 10:45:56
description: 
feature:
---

>环境：
AD：windows 2008 R2 x86_64：192.168.101.12：gz.newp.local
SVN：RHEL 6.3 x86_64：192.168.130.25
仓库：/data/gz/svn/it

### 安装
```
yum install httpd httpd-devel subversion mod_dav_svn

```
#### 确定已经安装了svn模块：mod_dav_svn
```
#cd /etc/httpd/modules
#ls | grep svn
mod_authz_svn.so
mod_dav_svn.so
```
如果要确认是否成功的安装了svn可以通过如下的命令进行验证：
```
svn –version

```

<!-- more  -->
#### 确认apache能正常启动
```
service httpd start
```
在浏览器中访问：http://localhost, 如果能看到Apache的页面，则说明Apache已经正常启动。


#### 进入到/etc/httpd/conf.d目录下用vim打开subversion.conf配置文件
一定要保证下面的两句话存在于配置文件中，否则无法加载svn的模块(一般用YUM安装的话，下面两个模块在安装的过程中已经改好了)。
```
LoadModule dav_svn_module     modules/mod_dav_svn.so
LoadModule authz_svn_module   modules/mod_authz_svn.so
```

### 建立仓库
```
#mkdir -p /data/gz/svn
#chown -R apache:apache /data                          //必须要把目录权限给apache，否则会报http 500内部服务器错误
#svnadmin create /data/gz/svn/it
#cd /data/gz/svn/it
#ls
conf     dav     db     format     hooks     locks     README.txt
```
如果目录下有这些文件，表示仓库创建成功。

### 编辑/etc/httpd/conf/http.d
```
# vim /etc/httpd/conf/http.d
NameVirtualHost *
<VirtualHost *>
    DocumentRoot /data/gz/svn/baomiban  //这里最好不要指向你所有的仓库，如果你指向了/data/gz/svn ,那么apache就不知道时候将请求提交给subversion模块
    ServerName svn.newp.local
    LogLevel warn
    ServerSignature on
    <Directory “/data/gz/svn”>
        Options Indexes FollowSymLinks MultiViews
        Order allow,deny
        allow from all
    </Directory>
</VirtualHost>
```

### 编辑ldap配置文件
```
# vim  /etc/openldap/ldap.conf
添加一行
REFERRALS       off
```

### 尝试用ldapsearch连接AD域控
```
ldapsearch -x -b “CN=administrator,CN=Users,DC=gz,DC=newp,DC=local” -D “svnuser@gz.newp.local”-W

```
这个命令的意思是：
使用svnuser的身份去查询gz.newp.local域中的Users这个OU里的administrator信息，然后返回一大堆东西，比较有用的就是其中的sAMAccountName，这个就是下文AuthLDAPURL中用到的关键字
命令的详细用法请点击《查询windows AD域的DN》查看

### 修改httpd.conf
就一下内容添加到上面新建的虚拟主机中
```
<Location /svn/>
    DAV svn
    SVNListParentPath On
    SVNParentPath /data/svn
    #SVNParentPath /data/svn/test
    AuthType Basic
    AuthName "Subversion repository"
    #AuthUserFile /data/svn/svn_user.conf
    AuthzSVNAccessFile /data/svn/svn_access.conf
    AuthBasicProvider ldap
    AuthzLDAPAuthoritative off
    AuthLDAPGroupAttribute
    AuthLDAPGroupAttributeIsDN on
    AuthLDAPURL "ldap://xxx.xxx.com/OU=xxx,DC=xxx,DC=com?sAMAccountName?sub?(objectClass=*)" NONE
    AuthLDAPBindDN "CN=test,CN=Users,DC=xxx,DC=com"
    AuthLDAPBindPassword "xxx"
    Require valid-user
    #Require ldap-group CN=users,OU=groups,DC=example,DC=com
</Location>
```
重启httpd
```
# service httpd restart
```

### 修改access.acl
```
# vim /data/gz/svn/access.acl
[groups]
admin=user1,test
everyone=user1,test
[/]
@admin=rw
* = r
[test:/]
@everyone=rw
*=rw
```

### 错误汇总

#### http 500 内部服务器错误
解决办法：
1.检查你的域用户是否在`OU=Users,DC=gz,DC=newp,DC=local`这个OU里
2.检查apache是否有权限
3.检查AuthLDAPBindDN指定的用户时候存在

#### http 403 禁止访问
解决办法：
1.检查access.acl文件，是否具有权限

#### http 401 未授权
查一下apache的error_log
```
[error] [client 192.168.101.234] user rajaruan not found: /svn/it
```
这个应该是ldap没有连接到域控制器，或者找不到`OU=Users,DC=gz,DC=newp,DC=local`这个OU
检查一下你的AuthLDAPURL是否正确。

参考：http://rajaruan.blog.51cto.com/2771737/1272761




