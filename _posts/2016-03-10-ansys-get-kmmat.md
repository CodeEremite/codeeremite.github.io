---
layout:      default
title:       在ANSYS中提取模型刚度阵、质量阵
keywords:    [ANSYS,刚度阵,质量阵]
description: 在ANSYS中使用APDL可以很方便的从模型数据文件(.full)或超单元文件(.sub)中提取模型的刚度阵、质量阵和阻尼阵。本文总结了使用APDL提取矩阵信息的方法，并对提取到的矩阵数据文件的格式(Harwell-Boeing format)进行了说明。
categories:  [CAE]
tags:        [有限元,CAE,ANSYS,Harwell-Boeing]
---

# 在ANSYS中提取模型刚度阵、质量阵

在ANSYS中使用APDL可以很方便的从模型数据文件(.full)或超单元文件(.sub)中提取模型的刚度阵、质量阵和阻尼阵。本文总结了使用APDL提取矩阵信息的方法，并对提取到的矩阵数据文件的格式(Harwell-Boeing format)进行了解释。

## 1. 使用APDL提取模型刚度阵、质量阵

提取矩阵的脚本如下，稍后解释。
    
    /SOLU                           ! 通过一次模态分析得到.full文件
    ANTYPE, MODAL
    MODOPT, LANB, 10, 0, 0, , OFF
    SOLVE

    /AUX2                           ! 进入二进制文件处理模块
    FILE,modelname,full             ! 操作对象(modelname.full)
    HBMAT, kkk, dat, , [ASCII/BIN], stiff, no, yes
    HBMAT, mmm, dat, , [ASCII/BIN], mass, no, no

.full文件是二进制格式的，其中包含组装后的模型刚度阵、质量阵和阻尼阵信息。

.full文件是后续步骤的操作对象。因此第一步要保证先有该文件。

.full文件是分析时由求解器生成的，所以你至少需要调用求解器进行一次分析。上述脚本的第一部分通过模态分析完成这个工作，当然你也可以通过其他分析来生成.full文件。如果已经有了.full文件，那该部分的脚本可以省略。


> HBMAT: 以[Harwell-Boeing](#HBformat)格式输出组装后的矩阵

HBMAT命令格式:
    
    HBMAT, Fname, Ext, --, Form, Matrx, Rhs, Mapping

    其中:

    Fname.Ext                   命令生成的文件名，即输出的矩阵文件名(默认working-directory/Jobname.MATRIX)
    --                          保留位
    Form      (ASCII/BIN)       输出文件是文本格式还是二进制格式
    Matrx     (STIFF/MASS/DAMP) 选择输出刚度阵、质量阵还是阻尼阵。生成full文件时分析类型必段保证有相关类型的矩阵信息 
    Rhs       (YES/NO)          是否输出方程的右端向量
    Mapping   (YES/NO)          是否输出节点自由度与矩阵下标的映射关系文件(文件名为Fname.MAPPING),默认不输出



<h2 id="HBformat">2. Harwell-Boeing Format</h2>

存储稀疏矩阵时为了节省空间，一般只存储矩阵中的非零值，但是只存储矩阵的非零值是不够的，还需要存储这些非零值的位置信息。

如果要存储一个矩阵，至少需要提供三个方面的信息

数据值、所在行、所在列

Harwell-Boeing格式存储稀疏矩阵用了三个一维数组：
列指针  A()
行索引  B()
非零值  C()

以对称阵为例，只存储下三角阵，只存非零值，按列的顺序从左往右存储，先从第一列开始，每个非零值的行存储在行索引数组。

列指针数组的第i个元素记录了在非零数组中第i列元素的位置，即

数组的C(A(i) : A(i+1)-1)属于第i列

列指针数 = 列数 + 1


矩阵类型用三个字符表示：

第一个字符:
    R     实数矩阵
    C     复数矩阵
    P     仅矩阵结构(无元素数值)
第二个字符:
    S     对称矩阵
    U     不对称矩阵
    H     Hermitian矩阵
    Z     病态对称矩阵
    R     带状矩阵
第三个字符:
    A     组装的矩阵
    E     单元矩阵(未组装)

对称矩阵只存储下三角矩阵



### 文本格式
[图-文本]

    line 1: (A72,A8)       文件的说明信息
    line 2: (5l14)         总行数(A)、列指针总行数(B)、行索引总行数(C)、矩阵元素总行数(D)、右端向量行数(E)
                           A = B + C + D + E    其中A不包括文件头,若E=0说明文件中不包括右端向量
    line 3: (A3,11X,4l14)  前三个字符表示矩阵类型
                           矩阵行数
                           矩阵列数
                           矩阵行索引数(对组装后的矩阵，值等于C)
                           单元元素数(组装的矩阵值为0)
    line 4: (2A16,2A20)    列指针格式、行索引格式、矩阵数值格式、右端项数值格式  
    line 5: (A3,11X,2I14)  如果不输出右端向量，没有此行(?)

第6行之后分成四个部分，分别为列指针、行索引、非零值部分、右边项

### 二进制格式

[ 图-二进制]

二进制文件与文本格式文件包含相同的内容，文本文件中用行作为内容的分隔符，二进制文件分隔方式不同。

二进制文件通过以下方法将文件内容分隔成不同的段：

1. 二进制文件由一系列段组成

2. 每段前后各包含4个字节，前后4个字节一样。4个字节表示一个32位整数，表示该段的长度（占多少字节)。

一段对应文本文件中的一行

第二段   总行数 4 bytes
         列指针 4 bytes
         行索引 8 bytes
         元素总行数 8 bytes
         右边项数 4 bytes

第三段   矩阵类型  3 bytes
         行数   4 bytes
         列数   4 bytes
         行索引   8 bytes
         单元元素数  4 bytes

第四段   16bytes 16bytes 20bytes 20bytes

列指针段中 每8个字节代表一个下标
行索引段中 每4个字节代表一个值
元素段中   每8个字节代表一个值，浮点数

### mapping-file格式一目了然
