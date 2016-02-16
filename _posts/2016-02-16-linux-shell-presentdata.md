---
layout:      default
title:       Linux Shell脚本编程——呈现数据 
keywords:    [shell,Present Data] 
description: 本文是我学习Linux Shell脚本编程的笔记。
categories:  [shell]
tags:        [shell]
---

# Linux Shell脚本编程——呈现数据 

## 关于呈现数据

(tee相当于一个Ｔ型接头，它将STDIN过来的数据同时发送给两个目的地，一个是STDOUT,一个是指定的文件名)

(tee -a testfile不会覆盖原文件，而是追加到文件中）

重定向.标准输入和输出.报告错误.舍弃数据.创建日志文件

标准文件描述符:

+ 0 标准输入　
+ 1 标准输出　
+ 2 标准错误

重定向时描述符与重定向符号间不能有空格

要将所有的输出重定向至同一个输入文件可以用&>

重定向到文件描述符时可以用>&2

在脚本中重定向输出有两种方法：

1. 临时重定向每行输出,每一行命令单独重定向
      如果运行脚本时重定向了STDERR，脚本中所有定向的STDERR的文本都会被重定向

2. 永久重定向脚本中的所有命令,使用exec命令,但无法轻易重定向回原来的位置

例:

    echo "在脚本中重定向输入"
    exec 0<manipulateuserinput
    count=1
    while read line
    do 
        echo "Line $count:$line"
        count=$[ $count+1 ]
    done 

还可以使用６个文件描述符(3-8),当作输入输出重定向都行

切换回原来的位置

    exec 3>&1
    exec 1>testout
    exec 1>&3

还可以创建读写文件描述符

关闭文件描述符： exec 3>&-　一旦关闭了文件描述符，就不能在再向它写入数据了

列出文件描述符 lsof命令

**注：阻止命令输出**

不想显示脚本输出在脚本在后台执行时很常见，如果脚本后台运行有错误，shell默认会通过电子邮件将它们发给进程的属主，这比较麻烦，尤其是小错误很多的时候

可以重定向至/dev/null，这是阻止错误信息而不保存它们的一个通用方法

可以不用删除文件再创建而清空该文件

    cat /dev/null > testfile

## 创建临时文件：

/tmp目录用来存放不用一直保留的文件，可以用mktemp在/tmp中创建一个唯一的临时文件，但不使用默认的umask值。它会将文件的读和写权限分配给文件的属主，一旦创建了文件，除了root用户，其他人无法访问它.

可以创建本地临时文件，后跟文件模板加上６个Ｘ,命令返回创建的文件名

    mktemp fileXXXXXX

加上选项-t 上面命令会强制在/tmp文件夹中生成文件，返回的是文件的完整路径

    mktemp -t fileXXXXXX

创建临时目录：

    mktemp -d dir.XXXXXX
