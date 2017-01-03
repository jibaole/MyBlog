---
title: tomcat的优化与内存监控
tags:
  - tomcat
categories:
  - Web server
toc: true
date: 2013-11-16 14:32:29
keywords:
  - tomcat
  - 优化
  - 内存监控
description:
  - tomcat
  - 优化
  - 内存监控
feature:
---

### 一、TOMCAT的优化

#### 1.内存设置(VM参数调优)
(1). Windows环境下，是tomcat解压版(执行startup.bat启动tomcat) ,解决办法:
修改“%TOMCAT_HOME%bincatalina.bat”文件，在文件开头增加如下设置：
``` 
set JAVA_OPTS=-Xms512m -Xmx512m -XX:PermSize=128M -XX:MaxNewSize=256m -XX:MaxPermSize=512m
```
备注：一定加在catalina.bat最前面。
<!-- more -->
(2). Windows环境下，是tomcat安装版(利用windows的系统服务启动tomcat),解决办法:
修改注册表HKEY_LOCAL_MACHINESOFTWARE/Apache Software Foundation/ProcrunTomcat6ParametersJavaOptions
原值为:
``` 
-Dcatalina.home=E:Tomcat 6.0
-Dcatalina.base=E:Tomcat 6.0
-Djava.endorsed.dirs=E:Tomcat 6.0commonendorsed
-Djava.io.tmpdir=E:Tomcat 6.0temp
-Djava.util.logging.manager=org.apache.juli.ClassLoaderLogManager
-Djava.util.logging.config.file=E:Tomcat 6.0conflogging.properties
```
加入：
```
Xms512m -Xmx512m -XX:PermSize=128M -XX:MaxNewSize=256m -XX:MaxPermSize=512m
```
重起tomcat服务,设置生效。

(3). Linux环境下, 解决办法:
修改“%TOMCAT_HOME%bincatalina.sh”文件，在文件开头增加如下设置：JAVA_OPTS=’-Xms256m -Xmx512m’
各参数详解：
-Xms：设置JVM初始内存大小(默认是物理内存的1/64)
-Xmx：设置JVM可以使用的最大内存(默认是物理内存的1/4，建议：物理内存80%)
-Xmn：设置JVM最小内存(128-256m就够了,一般不设置)
默认空余堆内存小于 40%时，JVM就会增大堆直到-Xmx的最大限制；空余堆内存大于70%时，JVM会减少堆直到-Xms的最小限制。因此服务器一般设置-Xms、 -Xmx相等以避免在每次GC 后调整堆的大小。
在较大型的应用项目中，默认的内存是不够的，有可能导致系统无法运行。常见的问题是报Tomcat内存溢出错误“java.lang.OutOfMemoryError: Java heap space”，从而导致客户端显示500错误。
-XX:PermSize ：为JVM启动时Perm的内存大小
-XX:MaxPermSize ：为最大可占用的Perm内存大小(默认为32M)
-XX:MaxNewSize，默认为16M
PermGen space的全称是Permanent Generation space,是指内存的永久保存区域，这块内存主要是被JVM存放Class和Meta信息的,Class在被Loader时就会被放到PermGen space中，它和存放类实例(Instance)的Heap区域不同,GC(Garbage Collection)不会在主程序运行期对PermGen space进行清理，所以如果你的应用中有很CLASS的话,就很可能出现“java.lang.OutOfMemoryError: PermGen space”错误。
对于WEB项目，jvm加载类时，永久域中的对象急剧增加，从而使jvm不断调整永久域大小，为了避免调整)，你可以使用更多的参数配置。如果你的WEB APP下都用了大量的第三方jar, 其大小超过了jvm默认的大小,那么就会产生此错误信息了。
其它参数：
-XX:NewSize ：默认为2M，此值设大可调大新对象区，减少Full GC次数
-XX:NewRatio ：改变新旧空间的比例，意思是新空间的尺寸是旧空间的1/8（默认为8）
-XX:SurvivorRatio ：改变Eden对象空间和残存空间的尺寸比例，意思是Eden对象空
间的尺寸比残存空间大survivorRatio+2倍（缺省值是10）
-XX:userParNewGC 可用来设置并行收集【多CPU】
-XX:ParallelGCThreads 可用来增加并行度【多CPU】
-XXUseParallelGC 设置后可以使用并行清除收集器【多CPU】

#### 2.修改tomcat让其支持NIO

修改前：
protocol=”HTTP/1.1″ connectionTimeout=”20000″ redirectPort=”8443″/>
修改成支持NIO的类型，配置如下 ：
protocol=”org.apache.coyote.http11.Http11NioProtocol ” connectionTimeout=”20000″ redirectPort=”8443″ />

#### 3.并发数设置

默认的tomcat配置，并发测试时，可能30个USER上去就当机了。
添加
maxThreads=”600″ //最大线程数
minSpareThreads=”100″ //初始化时创建的线程数
maxSpareThreads=”500″ //一旦线程超过这个值，Tomcat会关闭不需要的socket线程
acceptCount=”700″//指定当所有可以使用的处理请求的线程数都被使用时，可以放到
处理队列中的请求数，超过这个数的请求将不予处理
connectionTimeout=”20000″
redirectPort=”8443″ />
或者
name=”tomcatThreadPool” namePrefix=”catalina-exec-” maxThreads=”500″ minSpareThreads=”400″ />
executor=”tomcatThreadPool” port=”80″ protocol=”HTTP/1.1″ connectionTimeout=”20000″ enableLookups=”false”
redirectPort=”8443″ URIEncoding=”UTF-8″ acceptCount=”1000″ />

#### 4.Java虚拟机调优

应该选择SUN的JVM，在满足项目需要的前提下，尽量选用版本较高的JVM，一般来说高版本产品在速度和效率上比低版本会有改进。 JDK1.4比JDK1.3性能提高了近10%-20%，JDK1.5比JDK1.4性能提高25%-75%。

#### 5.禁用DNS查询

设置enableLookups=”false”：
enableLookups=”false” redirectPort=”8443″ URIEncoding=”UTF-8″ acceptCount=”1000″ />
当web应用程序向要记录客户端的信息时，它也会记录客户端的IP地址或者通过域名服务器查找机器名转换为IP地址。DNS查询需要占用网络，并且包括可能从很多很远的服务器或者不起作用的服务器上去获取对应的IP的过程，这样会消耗一定的时间。为了消除DNS查询对性能的影响我们可以关闭 DNS查询，方式是修改server.xml文件中的enableLookups参数值为false。

#### 6.设置解决乱码问题

URIEncoding=”UTF-8″ acceptCount=”1000″ />


### 二、TOMCAT的内存监控
1. 设置tomcat的perm size
2. 开启监控
在命令行输入jconsole，在弹出窗口中建立本地端口监控，如下图：
使用安装版Tomcat 6.0 ，打开tomcat界面选择java这一项，在java options:
加入
``` java

-Djava.rmi.server.hostname=127.0.0.1
-Dcom.sun.management.jmxremote.port=8088
-Dcom.sun.management.jmxremote.ssl=false
-Dcom.sun.management.jmxremote.authenticate=false
```
  使用jconsole 127.0.0.1:8088可以连接成功，也能看到jvm运行情况，但此时访问已经部署的应用，却提示“无法显示网页”今天又研究了一会，猜想了一下是不是这个端口独占的，不能和应用冲突，把Dcom.sun.management.jmxremote.port=8088 改为80，重启tomcat果然，应用可以访问。之后去网上看来些相关信息，确实为两个端口，不能占用。