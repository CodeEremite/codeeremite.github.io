---
title: Apache2.4.7(Ubuntu)配置
date: 2016-2-14 20:05:13 +0800
layout: default
categories: Apache2 
tags: Apache2 
---

# Apache2.4.7(Ubuntu)配置
Version:Apache 2.4.7(Ubuntu)

## apache2配置文件
主配置文件 /etc/apache2/apache2.conf,其他配置文件由apache2.conf(include)导入 
/etc/apache2/
    apache2.conf
    ports.conf
    /mods-enabled/*.load *.conf
    /conf-enabled/*.conf
    /sites-enabled/*.conf
其中:
        /etc/apache2/ports.conf     设置服务器端口
        /etc/apache2/conf-enabled/charset.conf 　网站字符编码配置
        
        /etc/apache2/envvars 环境变量
        /etc/apache2/conf-enabled/ 一般性配置文件
        /etc/apache2/mods-available/ 已安装的模块
        /etc/apache2/mods-enabled/ 已启用的模块
        /etc/apache2/sites-available/ 可用站点信息
        /etc/apache2/sites-enabled/ 已启用的站点信息


## 配置命令
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
Options Indexes 如果文件根目录中没有index.html,浏览器会显示文件根目录的目录列表，如果虚拟目录下没有index.html,浏览器也会显示虚拟目录的目录结构，要禁止显示目录结构列表，只需将Option中的Indexes去掉即可,或在Indexes前加上符号"-"

## 使用apache2ctl控制服务器
在Ubuntu中,apache2的配置依赖文件envvar,而该文件只在用初始化脚本(apachectl)启动apache2时才会激活。

apache2ctl是一个脚本文件，可供管理员控制服务器，但不是每个linux发行版中的apache http服务器都有这个文件　

apache2ctl 的启动参数：
    configtest  检查设置文件中的语法是否正确
    fullstatus  显示服务器完整的状态信息
    graceful    重启服务器，但不会中断原有的连接
    help        显示帮助信息
    restart     重启服务器
    start       启动服务器
    status      显示服务器摘要的状态信息
    stop        停止服务器
