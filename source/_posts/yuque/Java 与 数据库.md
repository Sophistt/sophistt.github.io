---
title: Java 与 数据库
urlname: alrs1tiuu3aulqpq
date: '2023-01-28 16:57:06 +0800'
tags: []
categories: []
---

## JDBC

JDBC API 允许用户访问任何形式的表格数据，尤其是存储在关系数据库中的数据。
资源申请和释放的顺序：

- 连接：Connection -> PreparedStatement -> ResultSet
- 关闭： ResultSet -> PreparedStatement -> Connection

## ORM

ORM 是指将数据库中的数据映射为对象， 程序即可以像操作对象一样操作数据的一种技术。依赖于 JDBC 和 DataSource。

- Hibernate：实现 ORM 的框架，类似于 MyBatis

## DataSource 对象

获取数据库连接的首选方法，有基础实现，连接池实现和分布式事务实现三种连接方法。

## Statement

Statement 是 java 执行数据库操作的一个接口，用于在已经建立数据库连接的基础上，向数据库发送要执行的 SQL 语句。具体用法为：

1. 首先导入 java.sql.\* 这个包。
2. 然后加载驱动，创建连接，得到 Connection 接口的的实现对象，比如对象名叫做 conn。
3. 然后再用 conn 对象去创建 Statement 的实例，方法是：Statement stmt = conn.creatStatement("SQL 语句字符串");
4. statement 对象将具体的 SQL 语句编译后发送到数据库。

### Statement

Statement 对象用于执行不带参数的简单 SQL 语句：Statement 每次执行 sql 语句，数据库都要执行 sql 语句的编译，最好用于仅执行一次查询并返回结果的情形，效率高于 PreparedStatement。但存在 sql 注入风险。

### PreparedStatement

PreparedStatement 是预编译执行的。在执行可变参数的一条 SQL 时，PreparedStatement 要比 Statement 的效率高，因为 DBMS 预编译一条 SQL 当然会比多次编译一条 SQL 的效率高。且安全性更好，有效防止 SQL 注入的问题。
对于多次重复执行的语句，使用 PreparedStatement 效率会更高一点。执行 SQL 语句是可以带参数的，并支持批量执行 SQL。由于采用了 Cache 机制，则预编译的语句，就会放在 Cache 中，下次执行相同的 SQL 语句时，则可以直接从 Cache 中取出来。
