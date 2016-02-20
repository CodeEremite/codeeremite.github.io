---
layout:      default
title:       MarkDown常用语法
keywords:    MarkDown
description: MarkDown的目标并不是要取代HTML，事实上Markdown只涵盖了一小部分HTML标签。准确地说，HTML是一种发布格式，而Markdown是一种书写格式，主要是为了方便写和编辑。本文概括了Markdown比较常用的语法，以备查用。 
categories:  [MarkDown]
tags:        MarkDown
---

# MarkDown常用语法

MarkDown的目标并不是要取代HTML，事实上Markdown只涵盖了一小部分HTML标签。准确地说，HTML是一种发布格式，而Markdown是一种书写格式，主要是为了方便写和编辑。本文概括了Markdown比较常用的语法，以备查用。

**需要知道的:**  

+    MarkDown只涵盖了一小部分HTML标签，对于未涵盖的标签，可以直接在文档中添加相应标签，不需要特别说明。   
     在MarkDown文档中使用HTML标签有一些限制:

     1. 块级别的HTML标签必须与相临的内容以空行隔开，而且标签的起始位置和结束位置不要有任何缩进;   
        块级别标签内不能混用MarkDown语句;
     3. 行标签可以用在MarkDown文档的任何位置，并且行标签内可以使用Markdown语法。  

+ 关于特殊字符的跳脱
     1. HTML中有两个很特殊的字符: & 和 <, 在HTML中如果要跳脱则应分别使用`&amp;`和`&lt;`。   
        HTML中即使在链接中也需要对这些特殊字符进行跳脱.
     2. MarkDown会根据这两个字符出现的位置，自动判断如何解释它们。  
        `&copy;` 会被解释为 &copy;  
        `AT&T`   会被解释为 AT&T。

> MarkDown中支持用反斜线跳脱的字符包括: `` \  ` * _ {} [] () # + - .  !``



## 1.块元素

### 段落

> 段落是用一个或多个空白行分开的连续文本行。

有几点需要注意的：

+   普通段落行首不应有缩进
+   两个连续(中间没有空行)的文本行会被视作一个段落,也就是说并不会讲自然断行视作`<br />`;
    如果要在行间插入换行符，应在上一行末尾添加两个或多个空白符。

### 标题

MarkDown支持两种形式的标题。  
第一种(Setext):

    This is a H1.
    ============
    This is a h2.
    ------------
    
注:

+ 该形式只支持到二级标题
+ 文本下方的等号、破折号不限数目

第二种(Atx):

    # This is H1
    ## This is H2
    ###### This is H6

注:

+ 最多支持6级标题

### 引用

MarkDown通过行首添加符号>来表示引用

注：

+ 可以只在首行添加;
+ 引用可以嵌套,行首>的个数表示嵌套的级别;
+ 引用可以包含其它的MarkDown元素，包括标题、列表、代码块等.

### 列表 

无序列表

    * 无序列表
    + 无序列表
    - 无序列表

有序列表

    1. 有序列表
    2. 有序列表
    8. 有序列表

注:

+ 标记后必须有空格
+ 有序列表前的具体数字不影响最后输出,最后输出只与列表项位置有关
+ 如果列表项间有空行，MarkDown在输出时会在列表项文本外包裹`<p>`标签
+ 列表项可以包含多行文本，每段文本需要缩进4个空格
+ 如果列表项中要添加引用，那引用前也要缩进
+ 如果列表项中要添加代码块，则代码块需要缩进8个空格

### 代码块

MarkDown通过对行缩进4个空格定义一个代码块,代码块一直持续到未缩进的行为止。

> MarkDown用`<pre>`和`<code>`包裹代码块

### 水平线

> 一行中三个或三个以上的-、\*或_解释为<hr />

符号间可以有空白

## 2.行内元素

### 链接

链接有两种：行内形式和参考形式, 两种形式的链接文本均用文括号包裹。

    行内形式:
        [link text](http://example.com/ "Optional Title Here")

    参考形式:
        [link text][id]
        [id]: http://example.com/ "Optional Title Here"

        省略id的形式:
        [link text][]
        [link text]: http://example.com/ "Optional Title Here"
        
    快捷方式,可应用于链接文本是链接自身或显示电子邮箱地址时的情况:
        <http://example.com/>
        输出为:<a href="http://example.com/">http://example.com</a>
        
注：

+ id忽略大小写
+ 第二种形式的链接第二行可以放在MarkDown的任何位置，一般集中放在段落后或文档末尾

### 强调

MarkDown通过星号、下划线来包裹需要强调的文本。

    1.单个星号、下划线包裹解释为<em>
        *text here*
        _text here*

    2.两个星号或下划线包裹解释为<strong>
        **text here**
        __text here__
        
注：

+ 如果星号或下划线前后均有空格，则将被视作普通字符，也可以用反斜线跳脱 

### 行内代码

行内代码用\`或\`\`包裹,MarkDown输出时在文本外包裹`<code>`标签

    输入:
    Use the `printf()` function.
    输出为:
    <p>Use the <code>printf()<code> function.</p>
    
    在行内代码中包含`时，可以用``包含代码文本,如：
    ``There is a literal backtick (`) here. ``

### 图像

与链接类似，也有两种形式，比链接多一个感叹号。
    
    行内形式:
        ![Alt text](/path/to/img.jpg "Optional title here")

    参考形式:    
        ![Alt text][id]
        [id]: /path/to/img.jpg "Optional title here"

注:

+   MarkDown语法并没有提供方法来指定图像大小等属性，如果需要指定这些属性，应该使用标准的`<img>`标签。
