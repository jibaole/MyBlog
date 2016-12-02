---
title: mongodb3.x配置文件的介绍
tags:
  - mongodb
categories:
  - Database
toc: true
date: 2015-11-17 11:19:06
description:
feature:
---

#### 概述
在启动mongod和mongos时可以通过配置文件来启动控制实例。
该配置文件包含的设置同等于mongod和mongos命令选项。

使用配置文件管理mongod和mongos更容易，特别是对于大规模部署。
还可以在配置文件中添加注释来解释服务器设置选项。

#### 使用配置文件
要启动mongod和mongos时使用配置文件，通过–config或者-f选项指定配置文件。
例：
```
mongod --config /etc/mongod.conf    //--config可以用-f替代
mongos --config /etc/mongos.conf
```
<!-- more -->
#### 文件格式
Mongodb的配置文件使用YAML格式。

注意：YAML不接受tab，请使用空格代替。

#### 引用
``` yaml
#此处为配置文件可配置的内容
#Mongod config file
#MongoDB configuration files use the YAML format.
#The following example configuration file contains several mongod settings.
#
########Example Start########
#systemLog:
#   destination: file
#   path: "/var/log/mongodb/mongodb.log"
#   logAppend: true
#storage:
#   journal:
#      enabled: true
#processManagement:
#   fork: true
#net:
#   bindIp: 127.0.0.1
#   port: 27017
#setParameter:
#   enableLocalhostAuthBypass: false
#
########Example End########
#
########Core Options
systemLog:
#   verbosity: 0    #Default: 0; 1 to 5 increases the verbosity level to include Debug messages.
#   quiet: <boolean>
#   traceAllException: <boolean>
#   syslogFacility: user
   path: "/usr/local/mongodb/log/mongod.log"
   logAppend: true
#   logRotate: <string>    #rename or reopen
   destination: file
#   timeStampFormat: iso8601-local
#   component:
#      accessControl:
#         verbosity: 0
#      command:
#         verbosity: 0
#      # COMMENT additional component verbosity settings omitted for brevity
#      storage:
#         verbosity: 0
#         journal:
#            verbosity: <int>
#      write:
#         verbosity: 0
#
#
########ProcessManagement Options
processManagement:
   fork: true
   pidFilePath: "/usr/local/mongodb/log/mongod.pid"
#
#
#########Net Options
net:
   port: 27017
#   bindIp: <string>    #Default All interfaces.
#   maxIncomingConnections: 65536
#   wireObjectCheck: true
#   ipv6: false
#   unixDomainSocket:
#      enabled: true
#      pathPrefix: "/tmp"
#      filePermissions: 0700
#   http:
#      enabled: false
#      JSONPEnabled: false
#      RESTInterfaceEnabled: false
#   ssl:
#      sslOnNormalPorts: <boolean>  # deprecated since 2.6
#      mode: <string>
#      PEMKeyFile: <string>
#      PEMKeyPassword: <string>
#      clusterFile: <string>
#      clusterPassword: <string>
#      CAFile: <string>
#      CRLFile: <string>
#      allowConnectionsWithoutCertificates: <boolean>
#      allowInvalidCertificates: <boolean>
#      allowInvalidHostnames: false
#      FIPSMode: <boolean>
#
#
########security Options
#security:
#   keyFile: <string>
#   clusterAuthMode: keyFile
#   authorization: disable
#   javascriptEnabled:  true
########security.sasl Options
#   sasl:
#      hostName: <string>
#      serviceName: <string>
#      saslauthdSocketPath: <string>
#
#
#########setParameter Option
setParameter:
   enableLocalhostAuthBypass: false
#   <parameter1>: <value1>
#   <parameter2>: <value2>
#
#
#########storage Options
storage:
   dbPath: "/data/db"
#   indexBuildRetry: true
#   repairPath: "/data/db/_tmp"
#   journal:
#      enabled: true
#   directoryPerDB: false
#   syncPeriodSecs: 60
   engine: "mmapv1"  #Valid options include mmapv1 and wiredTiger.
#########storage.mmapv1 Options
#   mmapv1:
#      preallocDataFiles: true
#      nsSize: 16
#      quota:
#         enforced: false
#         maxFilesPerDB: 8
#      smallFiles: false
#      journal:
#         debugFlags: <int>
#         commitIntervalMs: 100   # 100 or 30
#########storage.wiredTiger Options
#   wiredTiger:
#      engineConfig:
#         cacheSizeGB: <number>  #Default: the maximum of half of physical RAM or 1 gigabyte
#         statisticsLogDelaySecs: 0
#         journalCompressor: "snappy"
#         directoryForIndexes: false
#      collectionConfig:
#         blockCompressor: "snappy"
#      indexConfig:
#         prefixCompression: true
#
#
##########operationProfiling Options
#operationProfiling:
#   slowOpThresholdMs: 100
#   mode: "off"
#
#
##########replication Options
#replication:
#   oplogSizeMB: <int>
#   replSetName: <string>
#   secondaryIndexPrefetch: all
#
#
##########sharding Options
#sharding:
#   clusterRole: <string>    #configsvr or shardsvr
#   archiveMovedChunks: True
#
#
#########auditLog Options
#auditLog:
#   destination: <string>   #syslog/console/file
#   format: <string>   #JSON/BSON
#   path: <string>
#   filter: <string>
#
#
#########snmp Options
#snmp:
#   subagent: <boolean>
#   master: <boolean>
#
#
########mongos-only Options
#replication:
#   localPingThresholdMs: 15
#
#sharding:
#   autoSplit: true
#   configDB: <string>
#   chunkSize: 64
#
#
########Windows Service Options
#processManagement:
#   windowsService:
#      serviceName: <string>
#      displayName: <string>
#      description: <string>
#      serviceUser: <string>
#      servicePassword: <string>
```