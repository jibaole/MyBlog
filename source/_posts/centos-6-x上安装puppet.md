---
title: centos 6.x上安装puppet
tags:
  - puppet
categories:
  - Automated software
toc: true
date: 2016-08-18 14:58:53
description: 转自：http://www.how2centos.com/centos-6-puppet-install/
feature:
---

In this tutorial we’ll be covering the very basics of installing and configuring Puppet. Puppet is a system for automating system administration tasks. Its automation saves you countless hours of frustration, monotony and reinventing the wheel. It lets you perform administrative task from a central systems to any number of systems running any variant of operating system.
For a more complete description visit Puppet Labs.
### Installing the Puppet CentOS 6 packages
#### Install the Puppet Repository
``` bash
# rpm -ivh http://yum.puppetlabs.com/el/6/products/i386/puppetlabs-release-6-7.noarch.rpm
```
#### Install the EPEL x86_64 YUM Repository
```  bash
# rpm -Uvh http://download.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm
```
<!-- more -->
#### Install the Puppet Master packages

``` bash
# yum install puppet-server
```
 
#### Install the Puppet Client packages

``` bash
# yum install puppet
```
 
#### A Simple Manifest: Managing Ownership of a File
Step 1: Create a minimal manifest file called site.pp in /etc/puppet/manifests with the following content:
``` bash
# vi /etc/puppet/manifests/site.pp
```
 
``` 
# /etc/puppet/manifests/site.pp

import "classes/*"

node default {
    include sudo
 }
```
#### Step 2: Next create the sudo.pp class in /etc/puppet/manifests/classes/ with the following content:

``` bash
# vi /etc/puppet/manifests/classes/sudo.pp
```
 
``` bash
# /etc/puppet/manifests/classes/sudo.pp

class sudo {
    file { "/etc/sudoers":
        owner => "root",
        group => "root",
        mode  => 440,
    }
}
```
This class which will ensure that the owner, group, and mode of the /etc/sudoers file will be set consistently across all systems that belong to that class.
#### Step 3: Start the Puppet Master service and enable startup on boot
``` bash
# service puppetmaster start
# chkconfig puppetmaster on
```
 
Configuring Puppet
Configure the puppet client to connect to the server and enable logging. Edit the file /etc/sysconfig/puppet and uncomment the PUPPET_LOG and PUPPET_SERVER line specifying the servers address.
``` bash
# vi /etc/sysconfig/puppet
```

``` 
# The puppetmaster server
PUPPET_SERVER=PuppetMaster

# If you wish to specify the port to connect to do so here
#PUPPET_PORT=8140

# Where to log to. Specify syslog to send log messages to the system log.
PUPPET_LOG=/var/log/puppet/puppet.log

# You may specify other parameters to the puppet client here
#PUPPET_EXTRA_OPTS=--waitforcert=500
```

The client will automatically pull configuration from the server every 30 minutes, start it as a service and enable startup on boot

``` bash
# service puppet start
# chkconfig puppet on
```

#### Sign the SSL key request from the Puppet Client
In order for the two systems to communicate securely we need to create signed SSL certificates. You should be logged into both the Puppet Master and Puppet machines for this next step.
``` bash
# puppetca --list
server2.example.co.za
```

``` bash
# puppetca --sign server2.example.co.za
Signed server2.example.co.za
```
 