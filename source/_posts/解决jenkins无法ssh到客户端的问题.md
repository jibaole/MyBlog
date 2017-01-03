---
title: 解决jenkins无法ssh到客户端的问题
tags:
  - jenkins
categories:
  - Automated software
toc: true
date: 2015-11-11 11:25:25
description: 
  - jenkins
  - ssh
keywords:
  - jenkins
  - ssh
feature:
---

#### 报错信息
```
[09/21/15 13:34:40] [SSH] Checking java version of java
[09/21/15 13:34:41] [SSH] java -version returned 1.6.0_22.
[09/21/15 13:34:41] [SSH] Starting sftp client.
[09/21/15 13:34:42] [SSH] Copying latest slave.jar...
[09/21/15 13:34:52] [SSH] Copied 489,023 bytes.
Expanded the channel window size to 4MB
[09/21/15 13:34:52] [SSH] Starting slave process: cd "/home/sdmagg" && java -jar slave.jar
<===[JENKINS REMOTING CAPACITY]===>
hudson.util.IOException2: Slave JVM has not reported exit code. Is it still running?
        at hudson.plugins.sshslaves.SSHLauncher.startSlave(SSHLauncher.java:953)
        at hudson.plugins.sshslaves.SSHLauncher.access$400(SSHLauncher.java:133)
        at hudson.plugins.sshslaves.SSHLauncher$2.call(SSHLauncher.java:711)
        at hudson.plugins.sshslaves.SSHLauncher$2.call(SSHLauncher.java:696)
        at java.util.concurrent.FutureTask$Sync.innerRun(FutureTask.java:334)
        at java.util.concurrent.FutureTask.run(FutureTask.java:166)
        at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1145)
        at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)
        at java.lang.Thread.run(Thread.java:724)
Caused by: java.io.IOException: Remote call on sdapdevjsctrl failed
        at hudson.remoting.Channel.call(Channel.java:786)
        at hudson.slaves.SlaveComputer.setChannel(SlaveComputer.java:493)
        at hudson.slaves.SlaveComputer.setChannel(SlaveComputer.java:366)
        at hudson.plugins.sshslaves.SSHLauncher.startSlave(SSHLauncher.java:945)
        ... 8 more
Caused by: java.lang.ClassFormatError: Failed to load hudson.slaves.SlaveComputer$SlaveVersion
        at hudson.remoting.RemoteClassLoader.loadClassFile(RemoteClassLoader.java:340)
        at hudson.remoting.RemoteClassLoader.findClass(RemoteClassLoader.java:251)
        at java.lang.ClassLoader.loadClass(ClassLoader.java:321)
```


#### 解决办法
下载并解压java1.7.0_79到客户端的home目录下。
进入Jenkins，依次选择`Node => Manage => launch method => JavaPath`，将/home/{user}/jdk1.7.0_79/bin/java填写到JavaPath保存即可
