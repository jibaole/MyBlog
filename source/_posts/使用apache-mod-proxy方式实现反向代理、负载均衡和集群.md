---
title: 使用apache mod_proxy方式实现反向代理、负载均衡和集群
tags:
  - apache
categories:
  - Web server
toc: true
date: 2015-03-11 11:14:35
description: 
  - apache
  - mod_proxy
  - 反向代理
  - 负载均衡
  - 集群
keywords:
  - apache
  - mod_proxy
  - 反向代理
  - 负载均衡
  - 集群 
feature:
---

#### 安装mod_proxy
```
yum install mod_proxy_html
```

#### loadmodule
添加如下内容到http.conf：
```
LoadModule proxy_html_module modules/mod_proxy_html.so

```
重启apache
#### 反向代理的配置
正向代理一般是指PC连接到代理服务器，由代理服务器转发请求到网站。而反向代理指的是用户将请求发送给反向代理服务器，由反向代理服务器将请求路由转发到具体的服务器。
```
<VirtualHost *:80>
    ServerAdmin wanggq1989@gmail.com
    DocumentRoot /var/www/html/boxroom_longtush_com
    ServerName boxroom.longtush.com
    ErrorLog logs/boxroom_longtush_com-error_log
    CustomLog logs/boxroom_longtush_com-access_log common
    <Directory "/var/www/html/boxroom_longtush_com">
                AllowOverride All
   </Directory>
    ProxyPreserveHost On
    ProxyRequests Off
    ProxyPass / http://192.168.80.41:3002/
    ProxyPassReverse / http://192.168.80.41:3002/
</VirtualHost>
```

#### 负载均衡
负载均衡应该是最常用的横向扩展技术。Apache负载均衡将前端的请求，按照配置的比例转发给后台的Tomcat服务器，从而提高了并发访问量。作为mod_proxy方式，配置负载均衡十分简单，就是在反向代理的基础上，将请求发送到balancer。

1. 修改War包程序的web.xml文件，增加标识。
```
<Engine name="Catalina" defaultHost="localhost" jvmRoute="worker1">
```
2. 修改每个Tomcat服务器的server.xml,配置jvmRoute

3. 修改apache的httpd.conf文件，增加如下内容：
![](http://blog.itpub.net/attachment/201401/13/29254281_1389621679fiG0.png)
* 其中ProxyPass /images ! 表示/images开头的请求不会转发给Tomcat，而是作为静态资源在Apache的images目录下寻找资源文件
* 反向代理将所有的/Test请求都转发给名叫mycluster的balancer
* 这个balancer有两个Tomcat成员
* loadfactor表示请求的权值,该值默认为1，可以将该值设置为1到100之间的任何值。
* lbmethod表示负载均衡的算法,lbmethod可能的取值有：
  lbmethod=byrequests 按照请求次数均衡(默认)
  lbmethod=bytraffic 按照流量均衡
  lbmethod=bybusyness 按照繁忙程度均衡(总是分配给活跃请求数最少的服务器)
* stickySession=JSESSIONID表示开启粘性Session。他的意思是如果第一次请求分到了worker1的Tomcat,那么这个用户的后续请求，都会分配给worker1的这个Tomcat。
#### 集群
我觉得集群和负载均衡的区别就是集群包括Session复制和故障转移。 Session复制是广义的,实际上就是故障转移的时候,还可以继续读取这个用户的Session。 Session复制只是其中的一种方式，也可以采用Session服务器的方式。

在负载均衡的基础上配置集群。
1. 开启Tomcat的Session复制功能：
```
<Cluster className="org.apache.catalina.ha.tcp.SimpleTcpCluster" channelSendOptions="8"> 
          <Manager className="org.apache.catalina.ha.session.DeltaManager" 
             expireSessionsOnShutdown="false" 
             notifyListenersOnReplication="true"/> 
          <Channel className="org.apache.catalina.tribes.group.GroupChannel"> 
             <Membership className="org.apache.catalina.tribes.membership.McastService" 
                address="224.0.0.0" 
                port="45564" 
                frequency="500" 
                dropTime="3000"/> 
             <Receiver className="org.apache.catalina.tribes.transport.nio.NioReceiver" 
             address="192.168.1.100" 
             port="4000" 
             autoBind="100" 
             selectorTimeout="5000" 
             maxThreads="6"/> 
            <Sender className="org.apache.catalina.tribes.transport.ReplicationTransmitter"> 
                       <Transport className="org.apache.catalina.tribes.transport.nio.PooledParallelSender"/> 
                </Sender> 
            <Interceptor className="org.apache.catalina.tribes.group.interceptors.TcpFailureDetector"/> 
            <Interceptor className="org.apache.catalina.tribes.group.interceptors.MessageDispatch15Interceptor"/> 
        </Channel> 
        <Valve className="org.apache.catalina.ha.tcp.ReplicationValve" 
            filter=".*\.gif;.*\.js;.*\.jpg;.*\.png;.*\.htm;.*\.html;.*\.css;.*\.txt;"/> 
        <Valve className="org.apache.catalina.ha.session.JvmRouteBinderValve"/> 
          <ClusterListener className="org.apache.catalina.ha.session.JvmRouteSessionIDBinderListener"/> 
          <ClusterListener className="org.apache.catalina.ha.session.ClusterSessionListener"/> 
    </Cluster>
``` 
2. 在Apache配置故障转移(nofailover=Off)
![](http://blog.itpub.net/attachment/201401/13/29254281_1389627112RC2z.png)
配置了集群之后，请求从前端过来，假如分配到了worker1，那么因为粘性Session，他会一直访问worker1.
如果worker1宕机,这个用户的后续请求会自动转移到worker2.
由于Tomcat配置了Session复制,所以Session信息没有丢失。
整个过程对于用户是完全透明的。

参考：http://blog.itpub.net/29254281/viewspace-1070221/
