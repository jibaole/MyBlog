---
title: 基于elasticsearch+fluentd+kibana的日志系统搭建
tags:
  - fluentd
categories:
  - Big Data technology
toc: true
date: 2016-03-14 18:20:24
description:
feature:
---

#### 安装环境
>Centos 6.5 64位

#### 确保已经安装java
``` shell
$ java -version
java version "1.7.0_67"
Java(TM) SE Runtime Environment (build 1.7.0_67-b01)
Java HotSpot(TM) 64-Bit Server VM (build 24.65-b04, mixed mode)

```
<!-- more -->
#### 安装Elasticsearch
``` shell
$ curl -O https://download.elastic.co/elasticsearch/release/org/elasticsearch/distribution/tar/elasticsearch/2.3.3/elasticsearch-2.3.3.tar.gz
$ tar -zxvf elasticsearch-2.3.3.tar.gz
$ chmod 0755 elasticsearch-2.3.3

```

#### 后台启动Elasticsearch
``` shell
$ ./bin/elasticsearch -d

```


#### 访问Elasticsearch
http://192.168.80.41:9200 提示如下则表示正常安装启动
```
{
  "name" : "Toxin",
  "cluster_name" : "elasticsearch",
  "version" : {
    "number" : "2.3.3",
    "build_hash" : "218bdf10790eef486ff2c41a3df5cfa32dadcfde",
    "build_timestamp" : "2016-05-17T15:40:04Z",
    "build_snapshot" : false,
    "lucene_version" : "5.5.0"
  },
  "tagline" : "You Know, for Search"
}
```

#### 安装Elasticsearch 插件(可选)
```
$ ./elasticsearch-2.3.3/bin/plugin install lmenezes/elasticsearch-kopf/2.0
$ ./elasticsearch-2.3.3/bin/plugin install mobz/elasticsearch-head
```
  kopf(集群资源查询&数据查询):
  http://192.168.80.41:9200/_plugin/kopf
  head(主要用于es的数据查询一般配合kopf使用):
  http://192.168.80.41:9200/_plugin/head

#### 安装Kibana
```
$ curl -O https://download.elastic.co/kibana/kibana/kibana-4.5.1-linux-x64.tar.gz
$ tar -zxvf kibana-4.5.1-linux-x64.tar.gz
$ chmod 0755 -R kibana-4.5.1-linux-x64

```

#### 修改Kibana配置文件
```
$ vim /home/soft/kibana-4.5.1-linux-x64/config/kibana.yml 
# Kibana is served by a back end server. This controls which port to use.
 server.port: 5601
# The host to bind the server to.
 server.host: "192.168.80.41"
# If you are running kibana behind a proxy, and want to mount it at a path,
# specify that path here. The basePath can't end in a slash.
# server.basePath: ""
# The maximum payload size in bytes on incoming server requests.
# server.maxPayloadBytes: 1048576
# The Elasticsearch instance to use for all your queries.
 elasticsearch.url: "http://192.168.80.41:9200"
# preserve_elasticsearch_host true will send the hostname specified in `elasticsearch`. If you set it to false,
# then the host you use to connect to *this* Kibana instance will be sent.
# elasticsearch.preserveHost: true
# Kibana uses an index in Elasticsearch to store saved searches, visualizations
# and dashboards. It will create a new index if it doesn't already exist.
 kibana.index: ".kibana"
# The default application to load.
# kibana.defaultAppId: "discover"
# If your Elasticsearch is protected with basic auth, these are the user credentials
# used by the Kibana server to perform maintenance on the kibana_index at startup. Your Kibana
# users will still need to authenticate with Elasticsearch (which is proxied through
# the Kibana server)
#elasticsearch.username: "username"
#elasticsearch.password: "password"
# SSL for outgoing requests from the Kibana Server to the browser (PEM formatted)
# server.ssl.cert: /path/to/your/server.crt
# server.ssl.key: /path/to/your/server.key
# Optional setting to validate that your Elasticsearch backend uses the same key files (PEM formatted)
# elasticsearch.ssl.cert: /path/to/your/client.crt
# elasticsearch.ssl.key: /path/to/your/client.key
# If you need to provide a CA certificate for your Elasticsearch instance, put
# the path of the pem file here.
# elasticsearch.ssl.ca: /path/to/your/CA.pem
# Set to false to have a complete disregard for the validity of the SSL
# certificate.
# elasticsearch.ssl.verify: true
# Time in milliseconds to wait for elasticsearch to respond to pings, defaults to
# request_timeout setting
 elasticsearch.pingTimeout: 1500
# Time in milliseconds to wait for responses from the back end or elasticsearch.
# This must be > 0
 elasticsearch.requestTimeout: 30000
# Time in milliseconds for Elasticsearch to wait for responses from shards.
# Set to 0 to disable.
# elasticsearch.shardTimeout: 0
# Time in milliseconds to wait for Elasticsearch at Kibana startup before retrying
# elasticsearch.startupTimeout: 5000
# Set the path to where you would like the process id file to be created.
 pid.file: /var/run/kibana.pid
# If you would like to send the log output to a file you can set the path below.
# logging.dest: stdout
# Set this to true to suppress all logging output.
# logging.silent: false
# Set this to true to suppress all logging output except for error messages.
# logging.quiet: false
# Set this to true to log all events, including system usage information and all requests.
# logging.verbose: false
```

#### 启动Kibana
```
$ /home/soft/kibana-4.5.1-linux-x64/bin/../node/bin/node /home/soft/kibana-4.5.1-linux-x64/bin/../src/cli

```

#### 将Kibana启动进程添加到supervisor管理
``` shell
➜  hadoop-2.6.4 more /etc/supervisord.conf 
[unix_http_server]
file=/tmp/supervisor.sock
 
[supervisord]
logfile=/var/log/supervisord.log
logfile_maxbytes=50MB
logfile_backups=10
loglevel=warn
pidfile=/var/log/supervisord.pid
nodaemon=false
minfds=1024
minprocs=200
user=root
childlogdir=/var/log/
[rpcinterface:supervisor]
supervisor.rpcinterface_factory = supervisor.rpcinterface:make_main_rpcinterface
[group:countly]
programs=kibana
[program:kibana]
command=/home/soft/kibana-4.5.1-linux-x64/bin/../node/bin/node /home/soft/kibana-4.5.1-linux-x64/bin/../src/cli
directory=.
autorestart=true
redirect_stderr=true
stdout_logfile=/var/log/kibana.log
stdout_logfile_maxbytes=50MB
stdout_logfile_backups=50
stdout_capture_maxbytes=1MB
stdout_events_enabled=false
loglevel=warn
```


#### 安装fluentd
``` shell
$ curl -L https://toolbelt.treasuredata.com/sh/install-redhat-td-agent2.sh | sh

```

#### 后台启动fluentd
```
$ /etc/init.d/td-agent start 
Starting td-agent: [  OK  ]
$ /etc/init.d/td-agent status
td-agent (pid  21678) is running...

```


#### 安装fluent-plugin-elasticsearch和其他插件
```
$ /usr/sbin/td-agent-gem install fluent-plugin-elasticsearch
$ /usr/sbin/td-agent-gem install fluent-plugin-typecast
$ /usr/sbin/td-agent-gem install fluent-plugin-secure-forward

```

#### 解决gem源无法连接问题

```
sudo gem sources -l 
sudo gem sources -r http://rubygems.org
sudo gem sources -r https://rubygems.org
sudo gem sources -a https://ruby.taobao.org/

```

#### 配置td-agent.conf
```
<source>
  type http
  port 8888
  bind 0.0.0.0
  body_size_limit 32m
  keepalive_timeout 10s
  add_remote_addr true
  #cors_allow_origins ["http://192.168.80.92","http://192.168.80.11","http://192.168.88.87"]
</source>
<match fluent.**>
  type copy
# <store>
#   type file
#   path /var/log/td-agent/fluent.log
# </store>
  <store>
    type elasticsearch
    host 192.168.80.41
    port 9200
    include_tag_key true
      tag_key @log_name
    #path /home/soft/elasticsearch
    logstash_format true
    flush_interval 10s
  </store>
# <store>
#   type webhdfs
#   host 192.168.80.41
#   port 50070
#   path /log/razor/%Y%m%d/fluent.log.${hostname}
#   time_slice_format %Y%m%d
#     time_slice_wait 10m
#   time_format %Y-%m-%d %H:%M:%S
#   localtime
#   flush_interval 10s
# </store>
</match>
```

#### 启动fluentd
```
$ sudo /etc/init.d/td-agent start

```