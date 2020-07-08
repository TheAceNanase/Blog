# SQL-CREATE TABLE 语句



## SQL CREATE TABLE 语句

------

## SQL CREATE TABLE 语句

　CREATE TABLE 语句用于创建数据库中的表。

　表由行和列组成，每个表都必须有个表名。

### SQL CREATE TABLE 语法

```
CREATE TABLE table_name                
(                
column_name1 data_type(size),                
column_name2 data_type(size),                
column_name3 data_type(size),                
....                
);       
```

　column_name 参数规定表中列的名称。

　data_type 参数规定列的数据类型（例如 varchar、integer、decimal、date 等等）。

　size 参数规定表中列的最大长度。

　**提示：**如需了解 MS Access、MySQL 和 SQL Server 中可用的数据类型，请访问我们完整的 [数据类型参考手册](https://www.w3cschool.cn/sql/anioefpk.html)。

------

## SQL CREATE TABLE 实例

　现在我们想要创建一个名为 "Persons" 的表，包含五列：PersonID、LastName、FirstName、Address 和 City。

　我们使用下面的 CREATE TABLE 语句：

## 实例

CREATE TABLE Persons                
(                
PersonID int,                
LastName varchar(255),                
FirstName varchar(255),                
Address varchar(255),                
City varchar(255)                
);

　PersonID列数据类型为int，包含一个整数。

　LastName、FirstName、Address和City列具有包含字符的varchar数据类型，这些字段的最大长度为255个字符。

　空 "Persons" 表是这样的：

| PersonID | LastName | FirstName | Address | City |
| -------- | -------- | --------- | ------- | ---- |
|          |          |           |         |      |

　**提示：**使用 INSERT INTO 语句将数据写入空表。

​    

