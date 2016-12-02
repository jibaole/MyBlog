---
title: linux系统新硬盘分区并格式化
tags:
categories:
  - Linux server
description: 参考：https://www.qcloud.com/doc/product/213/2043#4.-.E6.A0.BC.E5.BC.8F.E5.8C.96.E5.88.86.E5.8C.BA
toc: true 
date: 2014-12-08 17:07:43
feature: 
---

## 小于2TB的硬盘

### 使用MBR分区表分区并格式化
#### 1. 查看数据盘信息
```
fdisk -l
```
![](https://mccdn.qcloud.com/img56a60467e297b.png)

#### 2. 数据盘分区
```
fdisk /dev/vdb
```
按照界面的提示，依次输入“n”(新建分区)、“p”(新建扩展分区)、“1”(使用第1个主分区)，两次回车(使用默认配置)，输入“w”(保存分区表)，开始分区。
这里是以创建1个分区为例，开发者也可以根据自己的需求创建多个分区。
![](https://mccdn.qcloud.com/img56a604c2b886f.png)
<!-- more -->
#### 3. 查看新分区
![](https://mccdn.qcloud.com/img56a605027a966.png)

#### 4. 格式化新分区
在进行分区格式化时，开发者可以自行决定文件系统的格式，如ext2、ext3等。本例以“ext3”为例：

使用下面的命令对新分区进行格式化。
```
mkfs.ext3 /dev/vdb1
```
![](https://mccdn.qcloud.com/img56a6053fb5aa0.png)

#### 5. 挂载新分区
创建mydata目录
```
mkdir /mydata
```
手动挂载新分区
```
mount /dev/vdb1 /mydata
```
出现如图信息则说明挂载成功，即可以查看到数据盘了。
![](https://mccdn.qcloud.com/img56a60615c0984.png)

#### 6. 添加分区信息
```
echo '/dev/vdb1 /mydata ext3 defaults 0 0' >> /etc/fstab
```
注意：如果没有添加分区信息，当服务器重启或开机时分区将不会自动挂载。
查看
```
cat /etc/fstab
```
出现如图信息则说明添加分区信息成功。
![](https://mccdn.qcloud.com/img56a606ad3180c.png)

## 大于2TB的硬盘

### 使用GPT分区表分区并格式化

#### 1. 查看磁盘列表
```
fdisk –l
```
![](https://mccdn.qcloud.com/img56a6086d43aa3.png)

#### 2. 创建GPT分区
使用parted工具，创建GPT分区。
![](https://mccdn.qcloud.com/img56a608a4b9d93.png)

#### 3. 查看新分区信息
![](https://mccdn.qcloud.com/img56a608e6c6545.png)

#### 4. 格式化分区
使用mkfs工具格式化分区。
![](https://mccdn.qcloud.com/img56a609267ccb7.png)

#### 5. 挂载新分区
格式化完成后，使用以下命令挂载新分区
```
mount 文件系统 分区路径 挂载点
```
此时使用以下命令可以查看到磁盘剩余容量
```
df –h
```
![](https://mccdn.qcloud.com/img56a60985596aa.png)

#### 6. 设置自动挂载
修改fstab文件，设置系统重启时自动挂载新分区，如图，加入最后一行内容。
![](https://mccdn.qcloud.com/img56a609b19718b.png)
