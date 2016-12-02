---
title: tree命令打印目录树结构
tags:
  - shell
categories:
  - Scripting language
toc: true
date: 2015-11-09 17:15:39
description: 
feature:
---

#### 1.打印目录树结构
```
$ tree -ah script
#-a 打印所有目录及文件，包括 （.）目录，-h打印每个目录及文件的大小
script
├── [ 122]  automate_expect.sh
├── [ 474]  backupFile.sh
├── [ 208]  checkword.sh
├── [ 266]  count.sh
├── [ 101]  debug.sh
├── [ 855]  factorial.sh
├── [ 193]  func2.sh
├── [ 369]  func3.sh
├── [ 161]  func4.sh
├── [ 429]  func5.sh
├── [ 309]  fun_call.sh
├── [4.0K]  haha
│   ├── [4.0K]  be1
│   │   └── [   0]  haha1.txt
│   └── [   0]  haha.txt
├── [ 449]  hanoi.sh
├── [  92]  hellofunc.sh
├── [ 210]  helloWorld.sh
├── [  20]  input.data
├── [ 101]  interactive.sh
├── [4.3K]  out.html
├── [ 602]  posi_args.sh
├── [ 277]  print1.sh
├── [ 207]  readName.sh
├── [ 888]  ret.sh
├── [ 12K]  .ret.sh.swp
├── [   0]  {seq
├── [  63]  shift_exam.sh
├── [ 328]  sleep.sh
├── [ 128]  userAdd.sh
└── [ 478]  xn.sh
```
#### 2.只打印目录文件
```
$ tree -dh script.
script
└── [4.0K]  haha
    └── [4.0K]  be1
```

参考：http://blog.itpub.net/29038506/viewspace-766717/