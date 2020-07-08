# SQL简介


## SQL简介

SQL（结构化查询语言）是用于访问和操作数据库中的数据的标准数据库编程语言。

SQL是关系数据库系统的标准语言。所有关系数据库管理系统(RDMS)，如MySQL、MS Access、Oracle、Sybase、Informix、Postgres和SQL Server都使用SQL作为它们的标准数据库语言。
    
为了处理数据库和数据库相关的编程，程序员需要有一些介质，或者可以说接口来详细说明一组命令或代码来处理数据库或访问数据库的数据。
    
## 你会从SQL中学到什么？

SQL为结构化查询语言提供了独特的学习和数据库处理技术，并将帮助您更好地控制SQL查询并有效处理这些代码。由于SQL帮助您包括数据库创建，数据库或表删除，获取行数据和修改这些数据等，并行SQL使得事情自动和平滑，最终用户可以轻松访问和处理该应用程序的数据。

 ## SQL 是什么？

* SQL 发音为“sequel”。
* SQL 指结构化查询语言，全称是 Structured Query Language（是最初由IBM开发）。
* SQL 是关系数据库系统的标准语言。
* SQL 是一种 ANSI（American National Standards Institute 美国国家标准化组织）标准的计算机语言。

 ## SQL 能做什么？

* SQL可以创建新的数据库及其对象（表，索引，视图，存储过程，函数和触发器）。
* SQL可以修改现有数据库的结构。
* SQL可以从数据库中删除（删除）对象。
* SQL可以TRUNCATE（删除）表中的所有记录。
* SQL可以对数据字典进行COMMENT。
* SQL可以RENAME一个对象。
* SQL可以从数据库中选择（检索）数据。
* SQL可以将数据插入到表中。
* SQL可以更新表中的现有数据。
* SQL可以从数据库表中删除记录。
* SQL可以在数据库中设置用户的GRANT和REVOKE权限。

## SQL 的历史

* 1970年，SQL由IBM的Donald D. Chamberlin和Raymond F. Boyce开发。
* 1974年，开发版本最初被称为SEQUEL（结构化英语查询语言）。
* 1979年，关系软件发布了第一个叫做System / R的商业产品。
* 由于商标冲突问题，SEQUEL首字母缩略词后来更改为SQL。
* 后来IBM基于System / R的原型开始在SQL上开发商业产品。
* 第一个关系数据库由RelationalSoftware发布，后来被称为Oracle。



    



 ## SQL 是一种标准 - 但是...


虽然 SQL 是一门 ANSI（American National Standards Institute 美国国家标准化组织）标准的计算机语言，但是仍然存在着多种不同版本的 SQL 语言。

然而，为了与 ANSI 标准相兼容，它们必须以相似的方式共同地来支持一些主要的命令（比如 SELECT、UPDATE、DELETE、INSERT、WHERE 等等）。



> 除SQL标准之外，大多数SQL数据库程序还具有自己的专有扩展名！




 ## 在您的网站中使用 SQL

要创建一个显示数据库中数据的网站，您需要：

* 一个RDBMS数据库程序（即MS Access，SQL Server，MySQL）。
* 使用服务器端脚本语言，如PHP或ASP。
* 使用SQL来获取所需的数据。
* 使用HTML / CSS来设置页面的样式



 ## RDBMS

RDBMS 指关系型数据库管理系统，全称 Relational Database Management System。

RDBMS 是 SQL 的基础，同样也是所有现代数据库系统的基础，比如 MS SQL Server、IBM DB2、Oracle、MySQL 以及 Microsoft Access。

RDBMS 中的数据存储在被称为表的数据库对象中。
表是相关的数据项的集合，它由列和行组成。

代码示例:

```
SELECT * FROM Customers;
```

每个表都被分解成称为字段的更小的实体。Customers表中的字段由CustomerID，CustomerName，ContactName，Address，City，PostalCode和Country组成。字段是表中的一列，用于维护表中每条记录的特定信息。

记录（也称为行）是表中存在的每个单独条目。例如，在上面的Customers表中有91条记录。记录是表中的横向实体。

列是表中的垂直实体，其包含与表中的特定字段相关联的所有信息。

## SQL进程

当您对任何RDBMS执行SQL命令时，系统将确定执行请求的最佳方式，并由SQL引擎确定如何解释该任务。

在此过程中包含了各种组件。

> 查询调度器优化引擎   
> 经典查询引擎   
> SQL查询引擎

典型的查询引擎处理所有非SQL查询，但SQL查询引擎不会处理逻辑文件。

## SQL标准命令

与关系数据库交互的标准SQL命令是创建、选择、插入、更新、删除和删除，简单分为以下几组：

### DDL（数据定义语言）

数据定义语言用于改变数据库结构，包括创建、更改和删除数据库对象。用于操纵表结构的数据定义语言命令有：

* `CREATE TABLE`-- 创建（在数据库中创建新表、表视图或其他对象）
* `ALTER TABLE`-- 更改 （修改现有的数据库对象，如表）
* `DROP TABLE`-- 删除  （删除数据库中的整个表、表或其他对象的视图）

### DML（数据操纵语言）

数据操纵语言用于检索、插入和修改数据，数据操纵语言是最常见的SQL命令。

数据操纵语言命令包括：

* `INSERT`-- 插入 （创建记录）
* `DELETE`-- 删除 （删除记录）
* `UPDATE`-- 修改（修改记录）
* `SELECT` -- 检索 （从一个或多个表检索某些记录）

### DCL（数据控制语言）

数据控制语言为用户提供权限控制命令。

用于权限控制的命令有：

* `GRANT`-- 授予权限
* `REVOKE`-- 撤销已授予的权限

## SQL格式化
使用[SQL格式化工具](https://www.w3cschool.cn/tools/index?name=sql_formatter)可以比较直观的看到您想要的操作
![](https://atts.w3cschool.cn/attachments/image/20180801/1533114262197328.png)
