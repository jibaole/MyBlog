---
title: shell实现文本多行变一行的几种方法
tags:
  - shell
categories:
  - Scripting language
toc: true
date: 2014-07-14 15:20:03
description:
feature:
---

有以下的文本内容：
``` shell
$cat test.txt
001
002
003
004
005
006
0000999
```
需要格式化成：
``` shell
001 002 003 004 005 006 0000999
```
下面介绍几种实现方法，供大家参考

* 方法一、最好的答案是waker老大提供的，就不解释了：
``` shell
xargs < test.txt
```
* 方法二、整个文件读入一个变量，然后直接打印，也很容易理解，dolphinlater提供：
``` shell
a=cat test.txt;echo $a
```

* 方法三、使用tr把换行符替换成空格：
``` shell
tr -s “n” ” ” < test.txt;echo
```

* 方法四、使用sed，把整个文件读入保持空间，处理最后一行的时候，替换所有换行符为空格，打印：
``` shell
sed -n ‘1h;1!H;${g;s/n/ /g;p;}’ test.txt
```

* 方法五、使用awk，读入一行打印一行，但是不打印换行符，最后一行多打印一个换行符：
``` shell
awk ‘{printf(“%s “,$0);}END{print}’ test.txt
```

* 方法六、使用paste命令格式化打印，-d指定分隔符，-s表示合并成一行：
``` shell
paste -d” ” -s – < test.txt
```

* 方法七、使用pr格式化打印，-s指定分隔符，-50指定每行打印多少域，-t指定取消页眉、页尾：
``` shell
pr -50t -s” ” test.txt
```