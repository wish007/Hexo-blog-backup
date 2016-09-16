---
title: 初识MySQL数据库
date: 2016-09-12 19:38:13
categories: SQL
tags:
- SQL
- MySQL
---

# SQL语句语法

## 结束SQL语句

- 多条SQL语句必须以分号`;`分隔

- mysql命令行必须使用分号来结束SQL语句

- MySQL不需要在单条SQL语句后加分号

  > 但某些DBMS可能强制要求加分号；所以，总是加上分号肯定没错。

## SQL语句大小写

- SQL 语句不区分大小写

  > SELECT、select和 Select 都是相同的。
  >
  > 许多 SQL 开发人员喜欢对所有 SQL 关键字使用大写，对所有列和表名使用小写，这样可以使代码更易于阅读和调试。
  >
  > 但是，MySQL 在4.1及之前的版本中对有些标识符（如数据库名、表名、列名）是区分大小写的。因此，最佳方式是按照大小写的惯例。

- 处理 SQL 语句时，其中所有的空格都会被忽略

  > SQL 语句可以在一行上给出，也可以分成许多行。显然，分成多行更便于阅读和调试。


<!--more-->


## SQL语句注释

- `-- `到该行结束；**注意：“--”后面至少要有一个空格**
- `/*...*/`行中、多行注释
- `#`在MySQL中可用，但这个不是标准的SQL注释


# SHOW语句

SHOW STATUS;显示广泛的服务器状态信息

SHOW GRANTS;显示授予用户的安全权限

SHOW ERRORS;显示服务器错误信息

SHOW WARNINGS;显示服务器警告信息

SHOW DATABASES;返回可用的数据库列表

SHOW TABLES;返回当前数据库内可用表的列表

SHOW COLUMNS FROM <customers>;返回<customers>表中每个字段的详细信息

其它：

USE <database>;选择/打开<database>数据库

HELP SHOW;进一步了解SHOW，显示允许的SHOW命令

# 常用关键字

DISTINCT  检索不相同的行

LIMIT  限制返回的行

ORDER BY  排序返回结果

DESC  降序排序

ASC  升序排序（默认）

WHERE  数据过滤

# SELECT语句

## 检索数据

使用SELECT检索数据，必须至少给出两条信息：

- 想选择什么

- 从什么地方选择

  SELECT <what> FROM <where>;

检索单列

```mysql
SELECT <column> FROM <tables>;
```

检索多列

```mysql
SELECT <column1>,<column2>,<column3> FROM <tables>;
```

检索所有列（使用通配符`*`）

```mysql
SELECT * FROM <tables>;
```

检索不相同的行（DISTINCT应用于所有列，不仅是前置它的列）
```mysql
SELECT DISTINCT <column> FROM <table>;
```

限制结果（返回限制的行）
```mysql
SELECT <column> FROM <table> LIMIT <n>;  # 返回前n行
SELECT <column> FROM <table> LIMIT <m>,<n>;  # 返回第m行后面的n行
```

## 排序检索数据
按列排序
```mysql
SELECT <column1> FROM <table> ORDER BY <column2>;  # 按列1的字母顺序排列列2的检索结果
```

按多列排序
```mysql
SELECT <column1>, <column2>, <column3>, FROM <table> ORDER BY <column4>, <column5>;  # 先按列4再按列5排序检索结果
```

> 注意：用非检索的列来排序数据也是合法的。

检索数据默认升序排序，降序排序必须指定 DESC关键字

```mysql
SELECT <column1>, <column2>, <column3>, FROM <table> ORDER BY <column4> DESC;
```
# WHERE字句

## 单WHERE字句

从表中检索出列中值等于5的结果；当然，`=`可以换成`<` `>` `!=` `<=` `>=` `BETWEEN`

```mysql
SELECT <column> FROM <tables> WHERE <column> = 5;
```

WHERE 后面可以跟 IS NULL 来进行空值检查

```mysql
SELECT <column> FROM <tables> WHERE <column> IS NULL;
```

## 组合WHERE字句

组合WHERE字句可以使用以下操作符：

AND 操作符

```mysql
SELECT <column> FROM <tables> WHERE <column> = 5 AND <column> = 10;
```
OR操作符

```mysql
SELECT <column> FROM <tables> WHERE <column> = 5 OR <column> = 10;
```
IN操作符

```mysql
SELECT <column> FROM <tables> WHERE <column> IN (5,10);
```
> IN 完成的功能和 OR 完全相同，但 IN 有以下优点：
>
> 更清楚、直观，IN操作符 比 OR 操作符执行更快，等等。

NOT操作符

```mysql
SELECT <column> FROM <tables> WHERE <column> NOT IN (5,10);
```

> **MySQL 中只支持 NOT 对 IN、BETWEEN 和 EXISTS 字句取反**，这个**多数其他 DBMS 允许使用 NOT 对各种条件取反**有很大的差别。