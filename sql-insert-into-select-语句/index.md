# SQL-INSERT INTO SELECT 语句



## SQL INSERT INTO SELECT 语句

------

　使用SQL，您可以将信息从一个表中复制到另一个表中。

　INSERT INTO SELECT 语句从表中复制数据，并将数据插入现有的表中。目标表中的任何现有行都不会受到影响。

### SQL INSERT INTO SELECT 语法

　我们可以将所有列从一个表中复制到另一个已经存在的表中：

```
INSERT INTO table2  SELECT * FROM table1;    
```

　或者我们可以把想要的列复制到另一个现有的表中：

```
INSERT INTO table2  (column_name(s)) SELECT column_name(s) FROM table1;   
```



## 演示数据库

------

　在本教程中，我们将使用著名的Northwind示例数据库。

　以下是"Customers"表中的数据：

| CustomerID | CustomerName                       | ContactName    | Address                       | City        | PostalCode | Country |
| ---------- | ---------------------------------- | -------------- | ----------------------------- | ----------- | ---------- | ------- |
| 1          | Alfreds Futterkiste                | Maria Anders   | Obere Str. 57                 | Berlin      | 12209      | Germany |
| 2          | Ana Trujillo Emparedados y helados | Ana Trujillo   | Avda. de la Constitución 2222 | México D.F. | 05021      | Mexico  |
| 3          | Antonio Moreno Taquería            | Antonio Moreno | Mataderos 2312                | México D.F. | 05023      | Mexico  |

　选自 "Suppliers" 表的数据：

| SupplierID | SupplierName               | ContactName      | Address        | City        | Postal Code | Country | Phone          |
| ---------- | -------------------------- | ---------------- | -------------- | ----------- | ----------- | ------- | -------------- |
| 1          | Exotic Liquid              | Charlotte Cooper | 49 Gilbert St. | Londona     | EC1 4SD     | UK      | (171) 555-2222 |
| 2          | New Orleans Cajun Delights | Shelley Burke    | P.O. Box 78934 | New Orleans | 70117       | USA     | (100) 555-4822 |
| 3          | Grandma Kelly's Homestead  | Regina Murphy    | 707 Oxford Rd. | Ann Arbor   | 48104       | USA     | (313) 555-5735 |



## SQL INSERT INTO SELECT 实例

------

　把 "Suppliers" 一栏复制到 "Customers" 一栏：

## 实例
```
INSERT INTO Customers (CustomerName, Country) SELECT SupplierName, Country FROM Suppliers;
```
　只将德国供应商的副本插入 "Customers" ：

## 实例
```
INSERT INTO Customers (CustomerName, Country) SELECT SupplierName, Country FROM Suppliers WHERE Country='Germany';
```
