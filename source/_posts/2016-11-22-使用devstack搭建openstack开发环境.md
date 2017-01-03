---
title: 使用devstack搭建openstack开发环境
tags:
  - openstack
categories:
  - Virtualization technology
toc: true
date: 2016-11-22 10:31:46
description: 
  - openstack
  - devstack
keywords:
  - openstack
  - devstack
feature:
---

#### 一.环境

Ubuntu server版最小化安装（只安装openssh-server）
网络： 
IP：192.168.16.128为外网IP，同时也作为管理网络IP 
网关：192.168.16.2（VMware虚拟机设置的，默认是这个）

#### 二.设置用户

1. 设置stack用户
``` bash
groupadd stack 
useradd -g stack -s /bin/bash -d /opt/stack -m stack 
passwd stack(设置密码）
```

2. 添加stack用户权限
``` bash
sudo echo "stack ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers 
```

3. 切换到stack用户
``` bash
sudo su stack （su stack  &&  cd)
```
<!-- more -->
#### 三.安装devstack

1. 安装git
``` bash
sudo apt-get install git
```

2. 在github上下载devstack代码
``` bash
sudo git clone https://github.com/openstack-dev/devstack -b stable/kilo 
```

3. 修改devstack权限，使stack用户能运行
``` bash
chown -R stack:stack /opt/stack/devstack
```

4. 打开devstack编辑local.conf（没有需新建）
```
[[local|localrc]]
# Define images to be automatically downloaded during the DevStack built process.
DOWNLOAD_DEFAULT_IMAGES=False
IMAGE_URLS="http://download.cirros-cloud.net/0.3.4/cirros-0.3.4-x86_64-disk.img"

#change github to trystack
GIT_BASE=http://git.trystack.cn
NOVNC_REPO=http://git.trystack.cn/kanaka/noVNC.git
SPICE_REPO=http://git.trystack.cn/git/spice/spice-html5.git

# Credentials统一设置登录密码
DATABASE_PASSWORD=pass
ADMIN_PASSWORD=pass
SERVICE_PASSWORD=pass
SERVICE_TOKEN=pass
RABBIT_PASSWORD=pass
#FLAT_INTERFACE=eth0

#主机IP,即访问openstack的IP
HOST_IP=192.168.16.128


# Enabling Neutron (network) Service
disable_service n-net
enable_service q-svc
enable_service q-agt
enable_service q-dhcp
enable_service q-l3
enable_service q-meta
enable_service q-metering
enable_service neutron

## Neutron options
Q_USE_SECGROUP=True
#floating ip网段
FLOATING_RANGE="192.168.16.0/24"  
#内网IP段
FIXED_RANGE="10.0.0.0/24"
#可用（可分配）floating IP网段
Q_FLOATING_ALLOCATION_POOL=start=192.168.16.102,end=192.168.16.110
#设置外网的网关（VMware自动默认的）
PUBLIC_NETWORK_GATEWAY="192.168.16.2"
Q_L3_ENABLED=True
#外部网卡（上外网的网卡)
PUBLIC_INTERFACE=eth0
Q_USE_PROVIDERNET_FOR_PUBLIC=True
#设置网桥br-ex以及网络映射
OVS_PHYSICAL_BRIDGE=br-ex
PUBLIC_BRIDGE=br-ex
OVS_BRIDGE_MAPPINGS=public:br-ex

# VLAN configuration.
Q_PLUGIN=ml2
ENABLE_TENANT_VLANS=True

# Work offline
#OFFLINE=True
# Reclone each time
RECLONE=yes

# Logging
# -------
# By default ``stack.sh`` output only goes to the terminal where it runs. It can
# be configured to additionally log to a file by setting ``LOGFILE`` to the full
# path of the destination log file. A timestamp will be appended to the given name.
LOGFILE=/opt/stack/logs/stack.sh.log
VERBOSE=True
LOG_COLOR=True
SCREEN_LOGDIR=/opt/stack/logs
```

5. 运行
``` bash
FORCE=True ./stack.sh
```

6. 如果不出意外，出现IP地址以及用户admin（demo）密码pass，则表示成功，登录192.168.16.128/出现如下界面： 
![](http://img.blog.csdn.net/20160521130153692)
如果出现错误执行下面语句重新来一次：
``` bash
./unstack.sh
FORCE=True ./stack.sh
```

转自：http://blog.csdn.net/svmachine/article/details/51469662