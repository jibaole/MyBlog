---
title: 'fatal error:allowed memory size ...报错的解决办法'
tags:
  - php
categories:
  - Web development
toc: false
date: 2015-04-17 11:05:55
description:
feature:
---

今天要用php代码来处理一个580M的日志文件，总共有219万多行记录，因为是.log的文件，在windows下面很难
Fatal error: Allowed memory size of 134217728 bytes exhausted (tried to allocate 2611816 bytes)，去百度了一下，原来是php.ini中的内存分配的问题，默认php代码能够申请到的最大内存字节数就是134217728 bytes，如果代码执行的时候再需要更多的内存，就会报错了，于是就将php.ini文件中的配置改了一下：memory_limit = 128M;将128M改成了256M
<!-- more -->
但是之后一想，一个php脚本一次请求的内存空间就要超过128M，那不管你以后将memory_limit设置成多大，以后肯定有出问题的时候。

究其原因，是我在在编码时，仅仅对变量赋值，却从来没有 unset ($var) 过。导致了内存占用越来越多，所以以后一个变量不再使用之后，一定要记得unset掉它。

下面附上我今天处理这个日志文件的代码：

``` php
<?php
set_time_limit(1800) ;
/**
 * 获取日志中发送失败的邮箱地址
 * @param $directory log日志的目录
 * @param $name    失败邮箱保存的文件名
 */function getmail($directory,$name){
//遍历目录下的.log文件
    $files=scandir("$directory");
    foreach($files as $v){
        if(preg_match_all("|mail\.log\D+|",$v,$log)){
            $logs[]=$log[0][0];
        }
    }
//将所有.log文件中发送失败邮箱提取出来    
    foreach($logs as $v){
        $row=file("$v");
        echo "读取".$v."文件<br />";
        foreach($row as $key => $value)
        {
            if(eregi("host name lookup failure|Connection timed out with|Connection refused by|cannot find your reverse hostname", $value)){
                if(preg_match("|\w+([-+.]\w+)*@\w+([-.]\w+)*.\w+([-.]\w+)*|", $row[$key],$matches)){
                    $mail[] = trim($matches[0]);
                    echo "获取发送失败的邮箱地址".$matches[0]."<br />";
                }else{
                    echo "无法获取日志中发送失败的邮箱，请检查";
                }
            }
        }
        unset($row);
    }
//将提取出来的发送失败邮箱写入到mail.txt文件中    
    $mailurl=fopen("$name","a");
    foreach($mail as $line)
    {
        fwrite($mailurl,$line."\r\n");
    }
    echo "将所有发送失败的邮箱地址写入".$name."<br />";
    fclose($mailurl);
}
getmail(".","mail.txt");
?>
```