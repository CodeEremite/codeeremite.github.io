---
layout:      default
title:       Linux Shell脚本编程——流程控制 
keywords:    [shell,Control Statement] 
description: 本文是我学习Linux Shell脚本编程的笔记。
categories:  [shell]
tags:        [shell]
---

# Linux Shell脚本编程——流程控制 

## 分支

### 1. if then 语句

+ 根据if行定义的命令退出状态码来确定是否执行then部分
+ 如果命令的退出码是０（即成功执行），则运行then部分的指令
+ 可以使用test命令来测试其他条件,但是未改变判断的本质

例:

    if date
    then 
       echo "it worked"
       echo "ok"
    fi

    if tttt;then 
       echo "it didn't work."
    fi

**关于test测试语句**

+ test命令提供了在if-then语句中测试不同条件的途径。如果test命令中列出的条件成立,test命令就会通出并返回状态码０，如果不成立，则返回退出状态码１。从而使得if-then语句能与其他编程语言类似的方式工作了。
+ test命令可以判断３类条件：数值比较；字符串比较；文件比较
+ test condition 可以有另一种方式[ condition ]（注意空格)
+ 两种布尔运算符可用：[ condition1 ] && [ condition2 ] 和||
   
例:

    if test 1 -eq 2
    then 
       echo "1==2"
    else
       echo "1!=2"
    fi
    if [ hello = hello ]
    then 
       echo "yes"
    fi
    if [ -f if_then_state ]; then
       echo "file exists"
    fi

### 2. if-then-else if-then-elif语句(多分支路径)

例:

    if tttt
    then 
       echo "tttt worked"
    elif date
    then
       echo "worked"
    else
       echo "tttt didn't work"
    fi

### 待学

1. 运行if语句中的那个命令生成的错误信息依然会显示在脚本的输出中
2. if-then的高级特性
    １）用于数学表达式的双尖括号
    ２）用于高级字符串处理功能的双方括号

### 3. case语句(分支语句的另一个选择) 

## 循环

### 1. for do语句

例:

    for test in I don't know if this'll work
    do
        echo work:$test
    done

上例有个小问题: **注意列表中的单引号，shell会尝试用他们定义一个单独的数据值**

有两种方法解决：

1. 使用转义字符（反斜线）将单引号转义
2. 使用双引号来定义用到单引号的值

改进后:

    for test in I don\'t know if "this'll" work;do
        echo work:$test
    done

for循环会假定列表值中每个值是用空格分隔的，如果值包含空格，必须得用双引号

也能从变量读取列表;也能从命令读取值

    list="Alabma Arizona"
    list=$list" China"
    for test in $list;do
        echo "Have you ever been to $test?"
    done

**C语言风格的for语句**

    echo "C-style For 语句"
    for((i=1,j=1;i<10;i++,j++))
    do 
        echo "The numbrer is $i,$j"
    done

### 待学

1. While命令,Until命令
2. 控制循环：break continue  
    break n可以跳出n层循环,continue也支持类似的用法
3. 在done后可以重定向命令以及管道

