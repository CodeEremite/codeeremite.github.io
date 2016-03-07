---
layout:      default
title:       MySQL基础
keywords:    MySQL
description: MySQL是采取C-S体系结构的软件。用户通过MySQL客户机或MySQL的外部编程接口(如PHP的MySQL处理函数)连接MySQL数据库服务器，通过服务器管理后面的数据库。本文总结了使用MySQL的一些基础知识。
categories:  [开发速查]
tags:        MySQL
---

# MySQL基础

MySQL是采取C-S体系结构的软件。用户通过MySQL客户机或MySQL的外部编程接口(如PHP的MySQL处理函数)连接MySQL数据库服务器，通过服务器管理后面的数据库。本文总结了使用MySQL的一些基础知识。

## 1.结构化查询语言SQL

> SQL是一种专门用于查询和修改数据库里的数据，以及对数据库进行管理和维护的标准化语言。

SQL包含四个部分:

+ 数据定义(DDL): 用于定义和管理数据对象，如数据库、数据表等(CREATE,DROP,ALTER等)
+ 数据操作(DML): 用于操作数据库对象中所包含的数据(INSERT,UPDATE,DELETE等)
+ 数据查询(DQL): 用于查询数据库对象中的数据，并将数据返回客户端(SELECT等)
+ 数据控制(DCL): 用来管理数据库，包括管理权限及数据更改(GRANT,REVOKE,COMMIT,ROLLBACK等)

## 2.MySQL常见操作(客户机端操作)

	# 连接数据库服务器
	$ mysql [-h 服务器主机地址] -u 用户名 -p[password] 

### 创建新用户并授权

语句格式:

	GRANT 权限 ON 数据库.数据表 TO 用户名@登录主机 IDENTIFIED BY "密码";

操作实例:

	mysql> GRANT SELECT,INSERT,UPDATE,DELETE ON *.* TO phpuser@"%" IDENTIFIED BY "brophp";
	# 更安全的方式是指定授权机器及具体的数据库
	mysql> GRANT SELECT,INSERT,UPDATE,DELETE ON mydb.* TO phpuser@localhost IDENTIFIED BY "brophp";

### 创建和删除数据库

操作:
	# 创建数据库
	mysql> CREATE DATABASE [IF NOT EXISTS] bookstore;
	
	# 删除数据库(不可恢复)
	mysql> DROP DATABASE [IF EXISTS] bookstore;
	
	# 显示所有已创建的数据库名称列表 
	mysql> SHOW DATABASES;
	
	# 打开数据库并作为当前(默认)数据库使用
	mysql> USE bookstore;

### 创建、编辑和删除数据表(数据表结构)

编辑表是指修改表的结构，包括添加字段、删除字段、修改列的类型、属性和索引，甚至可以修改表的名称。

	# 创建数据表
	mysql> CREATE TABLE [IF NOT EXISTS] [目标数据库.]表名称(
		-> 字段名1 列类型 [属性] [索引]，
		-> ...
		-> 字段名2 列类型 [属性] [索引]
	    -> )[表类型] [表字符集];

	# 更改数据表结构
	mysql> ALTER TABLE 表名 ACTION;

        # 常用ACTION:
        # 添加新字段
        mysql> ALTER TABLE 表名 ADD 字段名 <建表语句> [FIRST | AFTER 列名];
        
        # 修改列类型,CHANGE能更改字段名
        mysql> ALTER TABLE 表名 CHANGE 字段 新字段名 <建表语句>;
        mysql> ALTER TABLE 表名 MODIFY 字段 <建表语句>;

        # 修改表名
        mysql> ALTER TABLE 旧表名 RENAME AS 新表名;
	
	# 删除数据表
	mysql> DROP TABLE [IF EXISTS] 表名;
	
	# 显示当前数据库下所有数据表
	mysql> SHOW TABLES;
	
	# 查看数据表的详细结构 
	mysql> DESC book;
	

### 通过DML命令操作数据表中的数据记录(增、删、改)

	# 向数据表插入行记录(INSERT)
	mysql> INSERT INTO 表名[(字段名1,字段名2,...)] VALUES('值1','值2',...);
	
	# 更改数据表中存在的记录(UPDATE)
	mysql> UPDATE 表名 
        -> SET 字段名=表达式[, ......]      # 可对一列或多列数据进行修改
        -> [WHERE 条件]                     # 指定要更新的数据行
        -> [ORDER BY 字段]                  # 按照指定顺序更新行
        -> [LIMIT 行数];                    # 限制可以更新的行数目
	
	# 删除数表中的记录(DELETE)
	mysql> DELETE FROM 表名
        -> [WHERE 条件]
        -> [ORDER BY 字段]
        -> [LIMIT 行数];

### 通过DQL命令查询数据表中的数据

	# 从数据表中查询数据记录(SELECT)
	mysql> SELECT [ALL | DISTINCT] 
        -> {* | table.* | [table.]filed1 [AS alias1][,[table.]filed2 [AS alias2][,...]]}  # 选择哪些数据列
        -> FROM tableexpression[,...][IN externaldatabase]                                # 字段的来源
        -> [WHERE ...]              # 数据行必须满足哪些检索条件
        -> [GROUP BY ...]           # 指明按照哪几个字段来分组
        -> [HAVING ...]             # 过滤分组的记录，必须满足的次要条件
        -> [ORDER BY ...]           # 按一个或多个字段排序查询结果
        -> [LIMIT count];           # 对结果个数的限制
	
**FROM子句后包含一个或多个表达式，其中表达式可为单一表名称、已保存的查询或由INNER JOIN,LEFT JOIN或RIGHT JOIN得到的复合结果。**

**字段别名必须用单引号包裹**

### 查询优化

+ 最先出现的条件，是过滤和排除掉更多结果的条件，第二出现的次之，以此类推
+ ORDER BY中条件只与索引有关，与条件顺序无关
+ 多数情况下，根据WHERE条件的先后顺序和ORDER BY的排序字段先后顺序而建立的联合索引，就是与这条SQL语句匹配的最优索引结构
+ 尽管事实的产品不能只考虑一条SQL语句，也不能不考虑空间占用而建立太多的索引

**EXPLAIN语句可以验证索引是否合理**

## 3.数据表的设计
数据表由表结构和表内容两部分组成，以记录(行)和字段(列)组成的二维结构存储数据。    
数据表结构设计主要包括字段名称、字段类型和字段属性的设置。此外，为了确保数据的一致性和完整性，还需要使用约束、索引、主键和外键等功能属性。

### 3.1 字段类型
MySQL数据表是一个二维表，由一个和多个数据列构成，每个数据列都有它的特定类型，该类型决定了MySQL如何看等该列数据。  
MySQL的列类型有三种： 
   
1.数值类型的列  

+ 整型 (加上UNSIGNED属性可禁止负数)
	+ TINYINT (1 byte)
	+ SMALLINT (2 bytes)
	+ MEDIUMINT (3 bytes)
	+ INT (4 bytes)
	+ BIGINT (8 bytes)
+ 浮点型
	+ FLOAT (4 or 8 bytes)
	+ DOUBLE (8 bytes)
	+ DECIMAL (自定义形式)
	
2.字符串类型的列
  
+ CHAR 定长字符串
+ VARCHAR 变长字符串
+ TINYBLOB,TINYTEXT 非常小的BLOB(二进制大对象）和文本串
+ BLOB,TEXT 小BLOB和文本串
+ MEDIUMBLOB,MEDIUMTEXT 中等BLOB和文本串
+ LONGBLOB,LONGTEXT 大BLOB和文本串
+ ENUM 枚举
+ SET 集合

BLOB和TEXT类型是可以存放任意大数据的数据类型，只是前者区分大小写，后者不区分。  

ENUM和SET类型是特殊的串类型，其列值必须从固定的串集中选择，差别是前者必须选其中的一个值，而后者可以多选。

3.日期和时间类型的列  

+ DATE (3 bytes)
+ TIME (3 bytes)
+ DATETIME (8 bytes)
+ TIMESTAMP (4 bytes)
+ YEAR (1 byte)

### 3.2 字段属性

+ UNSIGNED 非负
+ ZEROFILL 自动用0补齐位数
+ AUTO_INCREMENT 自动增量,只能修饰整数类型
+ NOT NULL 必须添入值
+ DEFAULT 指定默认值

### 3.3 数据表的类型、存储位置、字符集

### 3.4 索引

在表字段中建立索引可以优化查询，确保数据的唯一性。

MySQL主要有四类索引:

+ 主键索引(PRIMARY KEY) 确定数据记录的位置，表内只能有一个主键
+ 唯一索引(UNIQUE) 主要为了避免数据出现重复，表内可有多个唯一索引
+ 常规索引(INDEX) 提升数据库的性能,但会多占用空间
+ 全文索引(FULLTEXT) 只能用于MyISAM表，只可在CHAR,VARCHAR或TEXT类型的列上创建

## 4.规范化

规范化理论用来改造关系模式，通过分解关系模式来消除其中不合适的数据依赖，以解决插入异常、删除异常、更新异常和数据冗余问题。

三大范式是数据库理论家总结的一个通用解决方案，用来指导数据库设计。

### 4.1 第一范式

+ 内容相似的数据列必须消除(再创建一个数据表来存放它们)
+ 必须为每一组相关数据分别创建一个数据表 
+ 每条数据记录必须用一个主键来标识

### 4.2 第二范式

+ 只要数据列里的内容出现重复，就意味着应该把数据表拆分为多个子表
+ 拆分形成的数据表必须用外键关联起来

### 4.3 第三范式

+ 与主键没有直接关系的数据列必须消除(再创建一个数据表存放它们)

### 4.4 范式的优缺点

缺点: 数据表个数越多，存入数据和查询数据复杂性越大,多个数据表一般比一个数据表提取数据慢

优点: 节省存储空间，能提供最灵活最丰富的查询选项

## 5. 数据库设计要求

+ 数据表里没有重复的冗余的数据
+ 数据表中没有order1,order2,order3等数据列
+ 全体数据表的空间占用总量越小越好
+ 使用频率高的数据库查询都能以简单高效的方式执行

注：

+ MySQL对数据库列的名字不区分字母的大小写形式，但对数据库和数据表的名字区分
+ 数据库、数据表和数据列的名字最多可以是64个字符长

