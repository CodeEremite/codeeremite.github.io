---
layout:      default
title:       Linux Shell脚本编程基础 
keywords:    [shell,basic] 
description: 本文是我学习Linux Shell脚本编程的笔记。
categories:  [shell]
tags:        [shell]
---

# Linux Shell脚本编程基础 

## 基本脚本结构  

最简单的脚本：

    #!/bin/bash
    echo "hello world!"
    exit 0

其中:

* 第一行必须指定执行本脚本的shell
* 一行间多条命令用分号隔开
* 最后一行指定脚本的退出状态码，shell脚本默认以最后一个命令的退出状态码退出，用exit [errorno]可以返回自己的退出状态码(该行不是必须的)

**关于退出状态码**

> 退出状态码是一个0～255之间的整数，由命令在运行结束时传递给shell,来告诉shell它完成了处理。shell通过专属变量 $? 来保存上个执行的命令的退出状态码，用户可以捕获这个值并在脚本中使用。 

可参考的退出状态码

    状态码     描述
     0       命令成功结束
     1       通用未知错误
     2       误用shell命令
     126     命令不可执行
     127     没找到命令
     128     无效退出参数
     128+x   Linux信号x的严重错误
     130     命令通过Ctrl+C终止
     255     退出状态码越界

## 变量

变量种类:

* 环境变量
* 用户变量

注: ${var}也可以用来引用变量的值，变量名两侧的花括号是辅助识别变量名

定义用户变量需要注意:

1. 变量名区分大小写,不超过２０个字符
2. **在变量、等号和值之间不能出现空格**
3. 变量的生命周期一直到脚本完成
4. 变量赋值不用加美元符号，如果引用则必须加美元符号，否则当作字符串

例:

    # 环境变量
    echo "User Name:$USER"
    echo "UID:$UID"
    echo "HOME:$HOME"

    #用户自定义变量
    var1=10
    var2="hello world"
    var3=$var2
    echo $var3 


**反引号允许将shell命令的输出赋给变量**

    #!/bin/bash
    testing=`date +%y年%m月%d日`
    echo "The date are:" $testing

## 输入输出重定向
* 输出重定向： > 重定向　　>>追加（重定向）
* 输入重定向:  <           <<内联重定向 
* 重定向是指文件重定向　内联重定向可以将命令行而不是文件指定为输入重定向的数据
* 内联重定向通过反映定一个文本标记来划分输入数据的开始和结尾


## 执行数学运算 

使用expr计算:

    #!/bin/bash
    #执行数学运算expr要注意空格，要赋值必须得用反引号
    aa=`expr 1 \* 5`
    echo $aa

    #另一种方式 $[... ]不用担心＊会被误解
    aa=$[1*5]
    echo $aa

使用bash计算器 bc

1. 输入bc -q可以去掉进入计算器时显示的欢迎信息
2. 用内建的scale变量来控制小数的位数 print打印变量和数字 quit退出计算器

在脚本中使用bc: 

    var1=`echo " scale=4; 3.44/5" | bc `
    echo $var1

**bc可以识别输入重定向，最好的办法是使用内联输入重定向**

    var1=3
    var2=5
    var3=`bc << EOF
    scale=4
    a1=$var1
    b1=$var2
    a1+b1
    EOF`
    echo The final answer for this is $var3

注意：在bc计算器中创建的变量只能在计算器中使用

