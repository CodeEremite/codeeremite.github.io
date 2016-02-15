---
layout:      default
title:       Linux Shell脚本编程——处理用户输入 
keywords:    [shell,UserInput] 
description: 本文是我学习Linux Shell脚本编程的笔记。
categories:  [shell]
tags:        [shell]
---

# Linux Shell脚本编程——处理用户输入 

从用户那儿获取数据的常用方式：

* 命令行选项
* 命令行参数
* 用户键盘输入

关于命令行参数:

1. $0是程序名，basename $0可以去掉路径
2. 通过变量名$1-$9 ${10} ...可以依次获取命令行参数
3. $#是参数的个数 ${!#}为最后一个参数
4. $*和$@提供了对所有参数的快速访问, 下面例子中加不加双引号效果是不一样的

例:

    #!/bin/bash
    for params in "$*"
    do 
       echo params:$params
    done
    echo "Test!"
    for params in "$@"
    do 
       echo params:$params
    done

移动变量,用命令shift

    echo shift命令
    count=1
    while [ -n "$1" ]
    do 
        echo "Parameter #$count=$1"
        count=$[ $count + 1 ]
        shift
    done

注：使用shift后，参数被移除掉后无法恢复，还可以shift n来指定移动的位数

**关于命令行选项**

处理shell脚本中的选项有３种方法:

1. 像处理命令行参数一样处理命令行选项。可以加上分隔符--来分隔选项和参数，也可以通过shift命令来处理带值的选项。但是有一个限制：无法处理合并选项的情况

2.  使用getopt命令 getopt options optstring parameters

    optstring是这个命令关键所在,它定义了命令行有效的选项字母，还定义了哪些选项字母需要参数值。在optstring中列出用到的每个选项字母，我后在每个需要参数的选项字母后加一个冒号。

    例： getopt ab:cd -a -b test1 -cd test2 test3
    
    结果：-a -b test1 -c -d -- test2 test3

    如果使用了不在optstring列出的选项，默认情况下该命令会产生一个错误信息，如果想忽略这条信息可以在getopt命令后加-q选项

    注：

    在脚本中使用： set -- `getopt -q optstring "$@"`

    原命令行参数会被替换为格式化后的命令行参数,但是getopt不擅长处理带空格的参数值。

3. 使用更高级的getopts命令

**关于用户输入**

    read命令 (要让echo显示后不换行，可以指定-n选项)
      -p    可以直接在read行指定提示符
      可以指定多个变量，也可以不指定变量（存在特殊变量REPLY中)
      -t    可以指定等待的秒数，当超时后，命令返回一个非零退出状态码
      -n1   当输入字符达到预设字符数后自动退出，赋值给变量
      -s    会阻止将传给read命令的数据显示在显示器上（本质上是文本颜色与背景色一致）
      也可以从文件中读取
