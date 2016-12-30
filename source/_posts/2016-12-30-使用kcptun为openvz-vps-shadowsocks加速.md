---
title: 使用kcptun为openvz vps shadowsocks加速
tags:
  - kcptun
  - shadowsocks
categories:
  - Network proxy
keywords:
  - kcptun
  - shadowsocks
  - openvz
description:
  - kcptun
  - shadowsocks
  - openvz
toc: true
date: 2016-12-30 14:15:12
thumbnail:
banner:
---
#### kcptun服务端支持的操作系统
CentOS 6+、Ubuntu 12+和Debian 7+

#### kcptun服务端一键安装脚本
1. 下载安装脚本并开始安装
``` bash
wget --no-check-certificate https://raw.githubusercontent.com/kuoruan/kcptun_installer/master/kcptun.sh
chmod +x ./kcptun.sh
./kcptun.sh update
./kcptun.sh
```
2. 设置 Kcptun 的服务端端口
``` 
请输入 Kcptun Server 端口 [1-65535]:
(默认: 29900):
```
  请输入一个未被占用的端口，Kcptun 运行时将使用此端口。
3. 设置加速的 IP
```
请输入需要加速的 IP [0.0.0.0 ~ 255.255.255.255]:
(默认: 127.0.0.1):
```
4. 设置需要加速的端口
```
请输入需要加速的端口 [1-65535]:
(默认: 12948):
```
5. 设置 Kcptun 密码
```
请输入 Kcptun 密码:
(如果不想使用密码请留空):
```
  为 Kcptun 单独设置一个密码，防止被他人恶意使用
6. 禁用压缩
```
是否禁用数据压缩?
(默认: 不禁用) [y/n]:
```
Kcptun 默认是启用压缩的，如果你这里设置为 y，也就是配置为 nocomp:true，那么就是禁用压缩。
<!-- more -->

#### 简易自我调优方法
1. 同时在两端逐步增大 client rcvwnd 和 server sndwnd
2. 尝试下载，观察如果带宽利用率（服务器＋客户端两端都要观察）接近物理带宽则停止，否则跳转到第一步

#### 各参数详细信息
https://github.com/xtaci/kcptun

#### 安装成功输出信息
``` 
恭喜, Kcptun 服务端配置完毕！
 
正在获取当前安装的 Kcptun 版本...
 
服务器IP:  10.10.10.10
端口:  29900
加速地址: 127.0.0.1:8388
密码: 123456
加密方式 Crypt:  salsa20
 
当前安装的 Kcptun 版本为: v20160922
 
推荐的客户端配置为: 
{
  "localaddr": ":8388",
  "remoteaddr": "10.10.10.10:29900",
  "key": "123456",
  "crypt": "salsa20",
  "mode": "fast",
  "mtu": 1350,
  "sndwnd": 1024,
  "rcvwnd": 1024,
  "datashard": 10,
  "parityshard": 3,
  "dscp": 0,
  "conn": 1,
  "autoexpire": 60,
  "nocomp": false
}
 
手机端参数可以使用：
-autoexpire 60 -key "123456" -crypt "salsa20"
 
其他参数请自行计算或设置, 详细信息可以查看: https://github.com/xtaci/kcptun
 
Kcptun 安装目录: /usr/share/kcptun
Kcptun 日志文件目录: /var/log/kcptun/
```

#### supervisor管理kcptun服务端
1. Supervisor 相关命令
``` bash  
service supervisord {start|stop|restart|status}
```
2. Kcptun 相关命令
``` bash
supervisorctl {start|stop|restart|status} kcptun
```
3. Supervisor 启动的时候会同时启动 Kcptun，运行 kcptun 相关命令时先确保 Supervisor 已启动。

#### 客户端配置
1. 下载一个管理kcptun客户端的工具(非kcptun客户端)
https://github.com/dfdragon/kcptun_gclient/releases
2. 下载服务端对应版本的 Kcptun客户端(版本根据服务端安装完成的输出日志获得)
当前安装的 Kcptun 版本为: v20160922
https://github.com/xtaci/kcptun/releases
3. 打开 Kcptun 启动工具，界面如下，请按序号操作
![](https://blog.kuoruan.com/wp-content/uploads/2016/08/Kcptun_GUI_config.png)
4. 可以参考如下配置编写config.json文件进行导入
``` json
{
  "localaddr": ":8388",
  "remoteaddr": "10.10.10.10:29900",
  "key": "123456",
  "crypt": "salsa20",
  "mode": "fast",
  "mtu": 1350,
  "sndwnd": 1024,
  "rcvwnd": 1024,
  "datashard": 10,
  "parityshard": 3,
  "dscp": 0,
  "conn": 1,
  "autoexpire": 60,
  "nocomp": false
}
```

#### 客户端几项说明
1. 本地监听端口，这个端口你可以随意设置，不是必须设置为 Shadowsocsk 的端口；
2. KCP服务器地址为你的服务器IP地址，不是 127.0.0.1，端口为服务端 Kcptun 的端口；
3. 如果你想使用IPv6协议，在填写服务器IP地址的时候需要用 []将IPv6地址括起来，如：[2000:0:0:0:0:0:0:1]；
4. 通信密钥是你配置的 Kcptun 密码，不是 Shadowsocks 的密码；在配置 Kcptun 的时候，不用管 Shadowsocks 的配置参数；
5. 参数区配置的时候，只需要配置你修改过的部分就行了，其他部分都不用改，除非你了解每项参数的意义；
6. 日志区非常重要，在排查问题的时候，这是必看部分；
7. 右下角为 Kcptun 最低需求版本，更新服务端之后，也需要更新本地客户端，只需要替换客户端文件即可。

#### kcptun客户端正常工作标志
Kcptun 启动工具中的日志区有类似下面的日志：
```
2016/09/24 11:57:15 stream closed
2016/09/24 11:57:15 stream opened
2016/09/24 11:57:17 stream closed
2016/09/24 11:57:17 stream closed
2016/09/24 11:57:18 stream closed
2016/09/24 11:57:19 stream opened
2016/09/24 11:57:19 stream closed
```

#### kcptun服务端正常工作标志
执行下面命令得到和客户端日志区类似日志即表示工作正常
```
./kcptun.sh log
```
参考：https://blog.kuoruan.com/110.html
优化参数及android客户端配置参考：https://blog.kuoruan.com/102.html