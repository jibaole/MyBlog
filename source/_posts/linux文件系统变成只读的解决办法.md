---
title: linux文件系统变成只读的解决办法
tags:
categories:
  - Linux server
toc: true
date: 2013-11-14 11:17:09
description:
feature:
---


#### mount
用于查看哪个模块输入只读，一般显示为：
```
/dev/hda1 on / type ext3 (rw)
none on /proc type proc (rw)
usbdevfs on /proc/bus/usb type usbdevfs (rw)
none on /dev/pts type devpts (rw,gid=5,mode=620)
/dev/hda5 on /home type ext3 (rw)
none on /dev/shm type tmpfs (rw)
/dev/hda2 on /usr/local type ext3 (rw)
/dev/nb1 on /EarthView/RAW type ext3 (ro)（变为只读了）

```


#### 如果发现有ro，就重新mount，或者umount以后再remount

#### umount /dev/nb1
如果发现有提示“device is busy”，找到是什么进程使得他busy
```
fuser -m /mnt/data 将会显示使用这个模块的pid
fuser -mk /mnt/data 将会直接kill那个pid
```
然后重新mount即可

#### 还有一种方法是直接remount
```
mount -o rw,remount /mnt/data

```
具体深入的做法，情况不同可以自行选择：
服务器/var/log/messages报错 ：
```
end_request: I/O error, dev sda, sector 122194293 Buffer I/O error on device sda1, logical block 446493 lost page write due to I/O error on sda1
```
下面是整个处理全过程
```
[root@php5 ~]# fdisk -lu #第一步 ：找出本地扇片所在的分区。
Disk /dev/sda: 73.4 GB, 73407868928 bytes
255 heads, 63 sectors/track, 8924 cylinders, total 143374744 sectors
Units = sectors of 1 * 512 = 512 bytes
Device Boot Start End Blocks Id System
/dev/sda1 * 63 4096574 2048256 83 Linux
/dev/sda2 4096575 75778604 35841015 83 Linux
/dev/sda3 75778605 129034079 26627737+ 83 Linux
/dev/sda4 129034080 143364059 7164990 5 Extended
/dev/sda5 129034143 139267484 5116671 83 Linux
/dev/sda6 139267548 143364059 2048256 82 Linux swap
[root@php5 ~]# tune2fs -l /dev/sda3 |grep “Block size” #找到block大小。
Block size: 4096
(122194293-75778605)*512/4096 =528691 利用公式算出逻辑块地址
b = (int)((L-S)*512/B)
[root@php5 ~]# debugfs
debugfs 1.35 (28-Feb-2004)
debugfs: open /deb/sda3
/deb/sda3: No such file or directory while opening filesystem
debugfs: open /dev/sda3
debugfs: icheck 582391
Block Inode number
582391 277584
debugfs: ncheck 277584
Inode Pathname
277584 /users/inn.net.cn/data/upload/download/innshow004.rar
debugfs: quit
[root@php5 ~]#dd if=/dev/zero of=/dev/sda1 bs=4096 count=1 seek=582391 #找到这个快的文件之后，需要做好备份，我们强制把它设置为0字节。
[root@php5 ~]# sync
```