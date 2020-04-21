---
title: JDBC使用
date: 2017-09-21 11:25:26
tags: [随笔,Java]
---

JDBC 全称为 Java Data Base Connectivity (java 数据库连接),可以为多种数据库提供统一的访问。
<!-- more -->

工具：Mysql(数据库),MyEclipse(开发工具),Navicat(数据库管理工具)

## 使用 JDBC 编程的步骤

1.加载驱动程序： `Class.forName(driverClass)
加载 Mysql 驱动： Class.forName(“com.mysql.jdbc.Driver”)`

2.加载 Oracle 驱动： `Class.forName(“oracle.jdbc.driver.OracleDriver”)`

3.获得数据库连接：
`DriverManager.getConnection(“jdbc:mysql://localhost:0000/XA”,”root”,”root”)`

4.创建 Statemen 对象： `conn.createStatement();`

采用 MVC 三层架构

- View （视图层)

- Control (控制层)

- Model (模型层) 数据库类和对数据库操作类

- DB (数据库）

## JDBC 各种连接方式的对比

- JDBC + ODBC 桥的方式
特点： 需要数据库的 ODBC 驱动，仅适用于微软的系统。

- JDBC + 厂商 API 的形式
特点：厂商 API 一般使用 C 编写。

- JDBC + 厂商 Database Connection Server + DataBase 的形式。
特点： 在 JAVA 与 DATABASE 之间架起了一台专用与数据库连接的服务器 (一般由数据库厂商提供)

- JDBC + DATABASE 的连接方式
特点：这使得 Application 与数据库分开，开发者只需关心内部逻辑的实现而不需注重数据库连接的具体实现。

