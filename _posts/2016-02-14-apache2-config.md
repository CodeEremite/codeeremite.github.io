---
layout:      default
title:       Apache2.4.7(Ubuntu)配置
keywords:    Apache2
description: Apache是当今最流行的服务器软件之一，可以运行在广泛的计算机平台上，本文是我在学习Apache配置的过程中作的一些笔记，以备后查。
categories:  [工具软件] 
tags:        [Apache2, Ubuntu] 
---

# Apache2.4.7(Ubuntu)配置

**注: 不同系统不同版本的apache配置文件管理方式有些许差别(差别主要在于配置文件具体结构，配置命令则是相同的），本文所述基于Apache 2.4.7(Ubuntu)**

## 1.配置文件结构

主配置文件 /etc/apache2/apache2.conf,其他配置文件在apache2.conf中通过include导入。 

配置文件的基本结构:  

    /etc/apache2/
        ./apache2.conf
        ./ports.conf
        ./mods-enabled/*.load *.conf
        ./conf-enabled/*.conf
        ./sites-enabled/*.conf

/etc/apache2文件夹下一些文件的用途:

    /etc/apache2/envvars                     环境变量,通过apache2ctl启动服务器时会读取该文件，默认配置文件中使用其中定义的环境变量
    /etc/apache2/ports.conf                  设置服务器监听端口
    /etc/apache2/conf-enabled/charset.conf 　网站字符编码配置
    /etc/apache2/conf-enabled/               一般性配置文件
    /etc/apache2/mods-available/             已安装的模块
    /etc/apache2/mods-enabled/               已启用的模块
    /etc/apache2/sites-available/            可用站点信息
    /etc/apache2/sites-enabled/              已启用的站点信息

Apache2根据功能将配置划分到多个文件中，这样做的好处是配置管理起来比较方便。

不同系统不同版本中Apache2的配置文件的组织方式稍有不同，在Apache2.4.7(Ubuntu)中没有httpd.conf文件,当然你可以新建一个httpd.conf文件，并在apache2.conf中通过include导入来实现你熟悉的配置方式,但个人认为没有这个必要。

另外，以sites-enabled文件夹为例，在终端通过命令可以发现sites-enabled中的文件只是一些连接文件，真身都在sites-available中。这么做的好处是，如果你要配置多个站点，只需在site-available文件中对每个站点新建一个站点配置文件，后续站点的启用和关闭只需在sites-enabled文件夹中建立和删除相应的连接文件即可，不用每次都修改配置文件。 


## 2.使用apache2ctl控制服务器

apache2ctl是一个脚本文件，供管理员控制服务器,**不是每个linux发行版中的apache http服务器都有这个文件**。　

在Ubuntu中,apache2的配置依赖文件envvars,而该文件只在用初始化脚本(apachectl)启动服务器时才会激活,如果直接用apache2启动，会报错。

    apache2ctl 的启动参数：
        configtest  检查设置文件中的语法是否正确
        fullstatus  显示服务器完整的状态信息
        graceful    重启服务器，但不会中断原有的连接
        help        显示帮助信息
        restart     重启服务器
        start       启动服务器
        status      显示服务器摘要的状态信息
        stop        停止服务器

## 3.apache2.conf中缺省的配置命令
1. IncludeOptional 与 Include 功能一样，在配置文件中导入其他配置文件,不同之处在于，使用IncludeOptional，当通配符没有任何匹配的文件和文件夹时，IncludeOptional指令会忽略而不是引发一个错误. 
2. ServerRoot 用来设置服务器所在目录，一般包含conf/、logs/等子目录。其他配置文件的相对路径基于此目录 
3. ServerAdmin 设置服务器返回客启端的错误信息中包含的管理员邮件地址，便于用户在收到错误信息后能及时与管理员取得联系
4. Mutex file 并行锁文件存放位置，本地文件夹，在envvar中定义
5. PidFile 记录启动时监控进程id的文件,在envvar中定义
6. Timeout 定义Apache在I/O时的等待时间

7. KeepAlive On/Off 是否允许一个持久的连接（一个连接处理超过一个请求）
8. MaxKeepAliveRequests 100 在一个连接中允许的最大请求数,如果设为0,则无限制数
9. KeepAliveTimeout 5 等待同一个连接中相同的客户端下一个请求的时间

10. User 
11. Group

12. HostnameLookups Off 记录客户端IP，而不是其网络名
13. ErrorLog 错误日志文件,如果在<VirtualHost>中未指定文件，则记录在全局log文件中
14. LogLevel 控制日志文件中信息的严格程度，也可以单独为某一个模块设置此项
    
15. <Directory> 控制服务器的默认安全模型,默认服务器对除/usr/share 和 /var/www 外的文件没有访问权限,其中/usr/share用于Web应用程序,/var/www用作Web服务器的本地文件夹,如果需要允许访问其他文件，需要在此处定义访问权限，或者在相关的虚拟主机设置中添加访问权限 
16. Options Indexes 如果文件根目录中没有index.html,浏览器会显示文件根目录的目录列表，如果虚拟目录下没有index.html,浏览器也会显示虚拟目录的目录结构，要禁止显示目录结构列表，只需将Option中的Indexes去掉即可,或在Indexes前加上符号"-"

## 4.虚拟主机

> 虚拟主机指的是在单一机器上运行多个网站。(这些站点运行在同一物理服务器上的事实不会明显的透漏给用户)

网上有人说如果只运行一个网站，那么不用虚拟主机,用全局设置即可。  
我没有试过，但是个人认为用虚拟主机来统一设置网站更好，这样仅一个网站的情况可以视为一种特例。  
更容易理解。

即 **要运行几个网站，就设置几个虚拟主机**


虚拟主机有两种:

1. 基于IP (一个IP一个站点)
2. 基于名称 (一个IP多个站点)

配置命令:

    <VirtualHost>
    ServerName
    ServerAlias
    ServerPath

如果要调试虚拟主机配置，使用命令行参数-S，这个命令会显示Apache如何解析配置文件。

**Apache决定请求文件位置的逻辑**

需要用到以下几个参数:

    URL_Path        用户请求的地址 
    ServerRoot      全局配置变量
    DocumentRoot    虚拟主机配置变量(即在<VirtualHost>中设置)
    DirectoryIndex  虚拟主机配置变量

规则:

**一般情况下,`DocumentRoot + URL_Path主机名端口号后面的部分`即构成了请求文件的路径。**

特殊情况:

1. 如果DocumentRoot配置时使用的是相对路径，那该路径是相对于ServerRoot   
    即: `ServerRoot + DocumentRoot + URL_Path主机名端口号后面的部分`

2. 如果URL_Path中用户请求的是一个文件夹，即URL_Path以`/`结尾，那么需要用到DirectoryIndex指定的文件。  
    即: `DocumentRoot + URL_Path主机名端口号后面的部分 + DirectoryIndex指定的文件`   
    Apache会按DirectoryIndex定义的文件顺序依次查找文件，如果都没找到，则根据第3节第16条命令决定是否显示文件目录列表。

**DocumentRoot(相对路径时ServerRoot+DocumentRoot)构成了用户从网上可见的基本文档树**



## 5.其他


