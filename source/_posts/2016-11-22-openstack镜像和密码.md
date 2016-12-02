---
title: openstack镜像和密码
tags:
  - openstack
categories:
  - Virtualization technology
toc: true
date: 2016-11-22 15:21:43
description: 转自：http://www.chenshake.com/openstack-mirror-and-password/
feature:
---

#### 虚拟机登陆方式
对Openstack来说：访问虚拟机提供了两种方式，

* ssh方式
* Dashboard的console。

Console方式，相当于你服务器前面直接登陆。有些虚拟机镜像处于安全考虑，不允许root的账号直接远程ssh登陆。

#### Ubuntu镜像
下载地址

http://cloud-images.ubuntu.com/precise/current/precise-server-cloudimg-amd64-disk1.img
<!-- more -->
这个是非常有名的镜像，大家用来测试也比较多。这个镜像默认是无法使用密码登陆，只能使用密钥登陆。所以你在console，也是无法登陆到虚拟机。

默认的用户名是：ubuntu，你需要使用密钥才能登陆。

如果你希望console可以登陆，那么你就需要想点办法

![](http://chenshake.qiniudn.com/wp-content/uploads/2013/11/Snap6_thumb.jpg)

这个时候，创建完的虚拟机，在console，就可以用 user：ubuntu，pass：ubuntu 登陆，不需要密钥。

不过这个时候，你还是无法通过ssh访问虚拟机，因为ssh默认设置是禁用密码认证，所以你需要多做点工作。
``` bash
#!/bin/sh
passwd ubuntu<<EOF
ubuntu
ubuntu
EOF
sed -i 's/PasswordAuthentication no/PasswordAuthentication yes/g' /etc/ssh/sshd_config
service ssh restart
```
这样你就可以使用ssh登陆到虚拟机，不需要通过console。

用root登陆

如果你希望直接使用root登陆，那么你就需要给root设置一个密码。

![](http://chenshake.qiniudn.com/wp-content/uploads/2013/11/Snap7.jpg)

这里设置的密码，就是镜像管理员的密码，也就是root的密码。

默认Redhat的RDO配置是不支持这个功能，你需要修改所有节点的nova.conf 文件
```
# Inject the admin password at boot time, without an agent.
# (boolean value)
#libvirt_inject_password=false
libvirt_inject_password=true
```
记住，是所有的节点，都是需要修改的。重启compute服务
```
/etc/init.d/openstack-nova-compute restart
```
当然你这样肯定还是无法ssh，使用root登陆，你就需要结合一下上面的一个例子，你就可以。

![](http://chenshake.qiniudn.com/wp-content/uploads/2013/11/Snap8.jpg)

就是输入下面的代码
``` bash
#!/bin/sh
sed -i 's/PasswordAuthentication no/PasswordAuthentication yes/g' /etc/ssh/sshd_config
service ssh restart
```
这个时候，你就可以ssh，用root的身份登陆进去。

#### Cirros
Openstack的开发，基本都使用这个image来测试，因为他比较小，只有10M。

镜像介绍 

镜像的地址：

https://launchpad.net/cirros/trunk/0.3.0/+download/cirros-0.3.0-x86_64-disk.img

Cirros，是可以使用用户名和密码登陆，也可以使用密钥登陆

user:cirros
password:cubswin:)

这个密码比较特别，所以你可以使用上面的方法来设置这个镜像的root的密码，实现ssh登陆。

#### CentOS Gold Image
国内用CentOS比较多，不过在Openstack上的CentOS image，以前都只能自己去制作。这个需要一个大家信任的第三方来完成。最近有了一个选择

http://catn.com/labs/centos-images/

http://catn.com/2013/04/18/building-a-virtual-machine-image-for-centos/

教你如何制作CentOS的image，并且提供现成的image下载

镜像下载地址：

http://mirror.catn.com/pub/catn/images/qcow2/centos6.4-x86_64-gold-master.img

镜像账号

user:root
pass:changeme1122
你是可以在创建虚拟机的时候，直接把这个root的密码修改。默认是运行root登陆。

不过这个image，没有集成cloud-init。导致你看到的虚拟机名字，不是你设置的计算机名。

 

#### 其他image资源
http://docs.openstack.org/image-guide/content/ch_obtaining_images.html

http://openstack.redhat.com/Image_resources

#### 附录
你需要设置安全组，打开22端口才能ssh登陆。
![](http://chenshake.qiniudn.com/wp-content/uploads/2013/11/Snap9.jpg)