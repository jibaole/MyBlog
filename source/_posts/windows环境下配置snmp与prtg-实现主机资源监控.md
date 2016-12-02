---
title: windows环境下配置snmp与prtg 实现主机资源监控
tags:
categories:
  - Monitoring technology
description: 参考：http://blog.chinaunix.net/uid-26149879-id-2559917.html
toc: true
date: 2012-07-08 15:52:51
feature:
---

#### snmp
1. SNMP，(简单网络管理协议)为应用层协议，是TCP/IP协议族的一部分。它通过用户数据报协议(UDP)来操作。在分立的管理站中，管理者进程对位于管理站中心的MIB的访问进行控制，并提供网络管理员接口。管理者进程通过SNMP完成网络管理。
2. 使用SNMP进行网络管理需要下面几个重要部分：管理基站，管理代理，管理信息库和网络管理工具。
3. SNMP的基本功能是：取得，设置和接收代理发送的意外信息。取得指的是基站发送请求，代理根据这个请求回送相应的数据，设置是基站设置管理对象(也就是代理)的值，接收代理发送的意外信息是指代理可以在基站未请求的状态下向基站报告发生的意外情况。
4. SNMP版本分为：V1 、V2 与最新的V3。
<!-- more -->

#### 在windows2003上安装snmp服务
1. 在系统中添加snmp服务
![](http://blog.chinaunix.net/attachment/201109/1/26149879_13148947900qoq.jpg)

2. 在系统服务里启动
若要配置 SNMP 代理信息，请按照下列步骤操作： 
 1）右键单击我的电脑，然后单击管理。 
 2）展开服务和应用程序节点，然后单击服务。 
 3）在“详细信息”窗格中，双击 SNMP 服务。 
![](http://blog.chinaunix.net/attachment/201109/1/26149879_13148948391kS1.jpg)
![](http://blog.chinaunix.net/attachment/201109/1/26149879_13148949034Z7e.jpg)
 

3. 配置snmp community(团体)和trap(陷阱)
![](http://blog.chinaunix.net/attachment/201109/1/26149879_13148951306suu.jpg)

4. 配置snmp安全性
![](http://blog.chinaunix.net/attachment/201109/1/26149879_1314895240R7L6.jpg)

#### prtg的配置
1. 打开PRTG主界面选自动搜索
![](http://blog.chinaunix.net/attachment/201109/1/26149879_1314895489c607.jpg)

2. 设置搜索条件
![](http://blog.chinaunix.net/attachment/201109/1/26149879_1314895551Juv2.jpg)

3. 搜索结果
![](http://blog.chinaunix.net/attachment/201109/1/26149879_1314895641688g.jpg)
![](http://blog.chinaunix.net/attachment/201109/1/26149879_1314895677lnvI.jpg)

4. PRTG主界面，可即时显示当前设备上的流量
![](http://blog.chinaunix.net/attachment/201109/1/26149879_1314895771LxlP.jpg)

5. 系统资源如CPU,硬盘，内存等需要自行添加，点击：Add sensor：
![](http://blog.chinaunix.net/attachment/201109/1/26149879_1314895855kaGf.jpg)
![](http://blog.chinaunix.net/attachment/201109/1/26149879_13148958580NmE.jpg)

6. 注意选择第三项
![](http://blog.chinaunix.net/attachment/201109/1/26149879_1314895941hNnB.jpg)

7. 填被管理设备信息
![](http://blog.chinaunix.net/attachment/201109/1/26149879_13148960355hz5.jpg)

8. 根据需要勾选所要监控的项目
![](http://blog.chinaunix.net/attachment/201109/1/26149879_1314896091XBre.jpg)

9. 最后效果
![](http://blog.chinaunix.net/attachment/201109/1/26149879_1314896131Kf6P.jpg)
