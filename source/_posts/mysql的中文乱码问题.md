---
title: mysql的中文乱码问题
tags:
  - mysql
categories:
  - Database
toc: true
date: 2015-05-17 10:56:16
description:
feature:
---

MySQL从4.x版本开始支持Unicode，3.x只有latin1编码。刚工作的时候就开始用MySQL了，用的php存取，网页xxx.php是gb2312的编码，存进去的数据用php取出来是中文，用phpMyAdmin执行select、update、dump都是中文，没有乱码问题。

从MySQL支持Unicode后，为了与时俱进，我们的web程序也开始考虑用UTF8了。其实UTF8也用了好几年了，程序基本能跑，没什么大问题，但是数据倒换的时候，总是遇到不爽的事情。
<!-- more -->
#### 问题现象
网页xxx.php用EditPlus另存为UTF8格式，MySQL在my.ini里设置default-character-set=utf8，建表时加了CREATE TABLExxx (myname varchar(255)) ENGINE=MyISAM DEFAULT CHARSET=utf8，用xxx.php执行insert/update/select出来的都是中文，貌似没问题，但是用phpMyAdmin看select是乱码，用第三方工具软件（如SQLyog）看select也是乱码，mysqldump也是乱码，很不爽。当然，如果你建表的时候，选择了binary/varbinary/blob类型，不会发现乱码，因为指定的是二进制保存，MySQL保存数据时就没有编码的概念了。

#### 查找问题 
虽然在my.ini里设置default-character-set=utf8，但是执行以下命令时有新发现：
``` sql
mysql> SHOW VARIABLES LIKE ‘character%’;
+—————————————-+————————-
| Variable_name            | Value
+—————————————-+————————-
| character_set_client       | latin1
| character_set_connection   | latin1
| character_set_database    | utf8
| character_set_filesystem    | binary
| character_set_results       | latin1
| character_set_server       | utf8
| character_set_system      | utf8
| character_sets_dir         | D:mysqlsharecharsets
+—————————————-+————————-
8 rows in set (0.00 sec)
 
mysql> SHOW VARIABLES LIKE ‘collation_%’;
+—————————————+——————
| Variable_name           | Value
+—————————————+——————
| collation_connection     | latin1_swedish_ci
| collation_database       | utf8_general_ci
| collation_server         | utf8_general_ci
+————————————–+——————
3 rows in set (0.00 sec)
```
发现Value列里面不全是utf8，仍然有部分是latin1，比如其中的client和connection。那网页xxx.php的工作过程就是这样的啦：从xxx.php页面上输入汉字，因为xxx.php是UTF8编码的，所以xxx.php以UTF8格式转换输入的汉字，然后以UTF8提交给mysql，但是mysql的client和connection都是latin1的，而表是UTF8的，所以mysql存储时，先将xxx.php提交的汉字，转成latin1的格式，再转成UTF8字符格式存在表中。如果此时我们用第三方软件或者phpMyAdmin去select查看此表，而表中存储的数据是被latin1过的UTF8字符，出来的时候是以UTF8格式取的，当然看起来时乱码了。解决方法就是让所有过程都是UTF8的就可以了。

#### 解决问题
1、从my.ini下手
```
[client]
default-character-set=utf8
[mysql]
default-character-set=utf8
[mysqld]
default-character-set=utf8
```
以上3个section都要加default-character-set=utf8，平时我们可能只加了mysqld一项。
然后重启mysql，执行
``` sql
mysql> SHOW VARIABLES LIKE ‘character%’;
mysql> SHOW VARIABLES LIKE ‘collation_%’;
```
确保所有的Value项都是utf8即可。
2、建表时加utf8，表字段的Collation可加可不加，不加时默认是utf8_general_ci了。
``` sql
CREATE TABLE `tablename4` (
`id` int(11) NOT NULL AUTO_INCREMENT,
`varchar1` varchar(255) DEFAULT NULL,
`varbinary1` varbinary(255) DEFAULT NULL,
PRIMARY KEY (`id`)
)  ENGINE=MyISAM  DEFAULT CHARSET=utf8
```
3、网页xxx.php保存时选择utf8编码，页头最好加上
``` php
header(‘conten-type:text/html;charset=utf-8’);
在执行CRUD操作前先执行一下

mysql_query(“set names utf8”);
测试代码xxx.php如下：
<?php
header(‘conten-type:text/html;charset=utf-8’);
mysql_connect(“localhost”, “root”, “password”) or die(“Could not connect: ” . mysql_error());
mysql_select_db(“test”);
mysql_query(“set names utf8”);
$str = “CHN 软件开发有限公司,JPN ソフトウェア開発株式会社,KOR 소프트웨어 개발 유한공사,RUS Суд программного обеспечения”.time();
$sql = “insert into tablename4 (varchar1, varbinary1 ) values (‘”.$str.”‘,'”.$str.”‘)”;
echo $sql.”<hr>”;
mysql_query($sql);
 
$result = mysql_query(“SELECT id, varchar1 ,varbinary1 FROM tablename4”);
while ($row = mysql_fetch_array($result, MYSQL_BOTH)) {
printf (“ID: %s , varchar1: %s, varbinary1: %s<br>”, $row[0], $row[“varchar1”], $row[“varbinary1”]);
}
 
mysql_free_result($result);
?>
```
如此设置之后，无论是在php页面插入任何utf8字符，在php页面里取出来的，在phpMyAdmin里取出来的，在mysql的第三方客户端软件里取出来的，都是一样的汉字了，不会再发现乱码，mysqldump出来的也是汉字。OK，问题解决。
【另】在中文windows系统下，在cmd.exe里运行mysql.exe字符终端，不能使用上面的规则，因为默认情况下，中文windows系统cmd.exe里的代码页是cp936即GBK，不能显示全部UTF8字符，所以在字符终端里看到乱码是正常现象，不要奇怪，这个问题在类Unix系统的shell终端里可以解决的。