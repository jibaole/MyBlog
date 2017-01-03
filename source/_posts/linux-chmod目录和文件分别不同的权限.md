---
title: linux chmod目录和文件分别不同的权限
tags:
categories:
  - Linux server
toc: false
date: 2014-11-18 16:16:59
keywords:
  - linux
  - chmod
description:
  - linux
  - chmod
feature:
---

在linux中权限是一个非常复杂的东西，如果权限没有设置好我们就无法对文件或目录进行操作了，下面我来介绍chmod命令对文件或目录权限设置
``` bash
法：chmod [who] [+ | - | =] [mode] 文件名
命令中各选项的含义为
u 表示“用户（user）”，即文件或目录的所有者。
g 表示“同组（group）用户”，即与文件属主有相同组ID的所有用户。
o 表示“其他（others）用户”。
a 表示“所有（all）用户”。它是系统默认值。
操作符号可以是：
+ 添加某个权限。
- 取消某个权限。
= 赋予给定权限并取消其他所有权限（如果有的话）。
设置mode所表示的权限可用下述字母的任意组合：
r 可读。
w 可写。
x 可执行。
X 只有目标文件对某些用户是可执行的或该目标文件是目录时才追加x 属性。
s 在文件执行时把进程的属主或组ID置为该文件的文件属主。方式“u＋s”设置文件的用户ID位，“g＋s”设置组ID位。
t 保存程序的文本到交换设备上。
u 与文件属主拥有一样的权限。
g 与和文件属主同组的用户拥有一样的权限。
o 与其他用户拥有一样的权限。
```
<!-- more -->
用命令chmod改变文件权限.有两种
* 第一种
```
chmod (ugoa) (=-+) (rwx) file
```
u代表文件属主,g代表文件属组,o代表系统中除属主和属组成员之外的其他用户,a代表所有用户(u,g,o)总和
+代表增加相应的权限,-代表减少相应的权限,=代表只有相应的权限
chmod g-w file就可以完成上面那个文件的权限改正.
* 第二种
用数字改正,r为4,w为2,x为1
把数字相加就是所对应的权限,不如rwx就是7,rw就是6
改正上面问题的权限方法为
chmod 775 file 就行了
偶尔要用到的命令，记录一下啊。
```
find /path -type f -exec chmod 644 {} ; 
find /path -type d -exec chmod 755 {} ;
```
示例：

（1）将档案 file1.txt 设为所有人皆可读取 :
```
       chmod ugo+r file1.txt 
```
（2）将档案 file1.txt 设为所有人皆可读取 :
```
       chmod a+r file1.txt  
```
（3）将档案 file1.txt 与 file2.txt 设为该档案拥有者，与其所属同一个群体者可写入，但其他以外的人则不可写入 :
``` 
       chmod ug+w,o-w file1.txt file2.txt 
```
（4）将 ex1.py 设定为只有该档案拥有者可以执行 :
```
       chmod u+x ex1.py 
```
（5）将目前目录下的所有档案与子目录皆设为任何人可读取 :
```
       chmod -R a+r * 
```
（6）收回所有用户的对file1的执行权限
```
       chmod a-x file1
``` 
使用数字来赋权
 
4.1 先看一下文件的权限格式
``` bash
[root@qs-wg-db2 scripts]# ll
total 20
-rw-r--r-- 1 oracle oinstall    0 Feb 24 00:00 alertlogbyday.log
-rwxr-xr-x 1 oracle oinstall  430 Feb 20 01:10 alertlogbyday.sh
-rwxr-xr-x 1 oracle oinstall    7 Feb 24 05:00 del_st_arch.log
-rwxr-xr-x 1 oracle oinstall  648 Feb 19 00:51 del_st_archive.sh
-rwxr-xr-x 1 oracle oinstall    9 Feb 24 05:00 max_sn.log
drwxr-xr-x 3 root   root     4096 Feb 23 23:40 pymonitor
```
ll的结果返回七列，分别表示如下含义：
第一栏  [文件属性]
第二栏  [文件数]
第三栏  [拥有者]
第四栏  [所有者群组]
第五栏  [大小]
第六栏  [建档日期]
第七栏  [档名]

范例 :将档案 file1.txt 设为所有人皆可读取 :
``` bash　
chmod ugo+r file1.txt
```　　
　　
将档案 file1.txt 设为所有人皆可读取 : 
``` bash
chmod a+r file1.txt
```
　　
将档案 file1.txt 与 file2.txt 设为该档案拥有者，与其所属同一个群体者可写入，但其他以外的人则不可写入 : 
``` bash　
chmod ug+w,o-w file1.txt file2.txt
```
　　
将 ex1.py 设定为只有该档案拥有者可以执行 : 
``` bash
chmod u+x ex1.py
```
　　
将目前目录下的所有档案与子目录皆设为任何人可读取 : 
``` bash
chmod -R a+r *
```
　　
此外chmod也可以用数字来表示权限如 chmod 777 file 
　　
1.语法为：chmod abc file
　　
　　其中a,b,c各为一个数字，分别表示User、Group、及Other的权限。
　　
　　r=4，w=2，x=1
　　若要rwx属性则4+2+1=7；
　　若要rw-属性则4+2=6；
　　若要r-x属性则4+1=5。
　　
　　范例：
``` bash
chmod a=rwx file
```
　　和
``` bash　　
chmod 777 file
```
　　效果相同
　　
``` bash
chmod ug=rwx,o=x file
```　　
　　和
``` bash　　
chmod 771 file
```　
　　效果相同
　　
　　若用chmod 4755 filename可使此程序具有root的权限

转自：http://www.111cn.net/sys/linux/55105.htm

