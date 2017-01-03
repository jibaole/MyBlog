---
title: ntpdate报no server suitable for synchronization found的解决办法
tags:
categories:
  - Linux server
toc: true
date: 2013-11-16 17:09:28
keywords:
  - ntpdate
  - synchronization
description:
  - ntpdate
  - synchronization
feature:
---

### 错误的原因

#### 1.Server dropped: Strata too high
在ntp客户端用ntpdate –d serverIP查看，发明有“Server dropped: strata too high”的错误，并且显示“stratum 16”。而正常景象下stratum这个值得局限是“0~15”。
这是因为NTP server还没有和其自身或者它的server同步上。
以下的定义是让NTP Server和其自身对峙同步，若是在/etc/ntp.conf中定义的server都不成用时，将应用local时候作为ntp办事供给给ntp客户端。
```
server 127.127.1.0 
fudge 127.127.1.0 stratum 8
```
原因分析：
在ntp server上从头启动ntp服务后，ntp server自身或者与其server的同步的须要一个时候段，这个过程可能是5分钟，在这个时候之内涵客户端运行ntpdate号令时会产生no server suitable for synchronization found的错误。
<!-- more -->
那么如何知道何时ntp server完成了和自身同步的过程呢？
在ntp server上应用命令：
``` bash
＃ watch ntpq -p
```
呈现画面：
``` bash
＃ ntpq -p                                                                                                             
 Thu  Jul 10 02:28:32 2008 
     remote           refid      st t when poll reach   delay   offset jitter 
============================================================================== 
192.168.30.22   LOCAL（0）         8 u   22   64    1    2.113 179133.   0.001 
LOCAL（0）        LOCAL（0）        10 l   21   64    1    0.000   0.000  0.001
```
重视LOCAL的这个就是与自身同步的ntp server。
重视reach这个值，在启动ntp server办过后，这个值就从0开端络续增长，当增长到17的时辰，从0到17是5次的变革，每一次是poll的值的秒数，是64秒*5=320秒的时候。
若是之后从ntp客户端同步ntp server还失败的话，用ntpdate –d来查询具体错误信息，再做断定。

#### 2.Server dropped: no data
呈现这个题目的原因可能有2：

* 搜检ntp的版本，若是你应用的是ntp4.2（包含4.2）之后的版本，在restrict的定义中应用了notrust的话，会导致以上错误。
* 搜检ntp server的防火墙。可能是server的防火墙樊篱了upd 123端口。
``` bash
＃service iptables stop
```
来关掉iptables办过后再测验测验从ntp客户端的同步，若是成功，证实是防火墙的题目，须要更改iptables的设置。


### 服务器
```
stdtime.gov.hk 210.0.235.14(香港授时中心的官方时间同步服务器IP域名)
time.stdtime.gov.tw 59.124.196.84 (台湾授时中心的官方时间同步服务器IP域名)192.43.244.18 (美国授时中心的官方时间同步服务器IP域名)
210.72.145.44 (中国西安授时中心的官方时间同步服务器IP域名)
# ntpdate -u 192.43.244.18 210.72.145.44  210.0.235.14  59.124.196.84 
# ntpdate 133.100.9.2
*/30 * * * * /usr/sbin/ntpdate  stdtime.gov.hk
 210.72.145.44是中国国家授时中心的官方服务器
133.100.9.2    来自：日本国家授时中心的官方服务器
```

#### 1. 查看当前时区
``` bash
# cat /etc/sysconfig/clock
```
#### 2. 修改设置时区
* 方法(1)
``` bash
# tzselect
```


* 方法(2)
仅限于RedHat Linux 和 CentOS) 这种方法在rhel4u4中没看到系统命令
``` bash
# timeconfig
```
#### 3. 复制相应的时区文件，替换系统默认时区
``` bash
# cp /usr/share/zoneinfo/$主时区/$次时区 /etc/localtime
对于中国服务器则执行：
# cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
```
#### 4. 将当前时间写入BIOS永久生效（避免重启后失效）
``` bash
# hwclock
/usr/sbin/ntpdate -u 192.43.244.18 210.0.235.14 210.72.145.44
```
修改linux时区

#### 5.定时同步时间
``` bash
*/20 * * * * ( /usr/sbin/ntpdate -u 192.43.244.18 210.0.235.14  210.72.145.44 2 ) && ( hwclock --systohc )>>
```

