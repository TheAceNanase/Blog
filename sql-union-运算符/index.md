# SQL-UNION 运算符



## SQL UNION 运算符

------

　　UNION运算符用于组合两个或更多SELECT语句的结果集，而不返回任何重复的行。

- UNION中的每个SELECT语句必须具有相同的列数
- 这些列也必须具有相似的数据类型
- 每个SELECT语句中的列也必须以相同的顺序排列
- 每个SELECT语句必须有相同数目的列表达式
- 但是每个SELECT语句的长度不必相同

### SQL UNION 语法1

```
SELECT column_name(s) FROM table1
UNION
SELECT column_name(s) FROM table2;
```

> **注释：**默认情况下，UNION 运算符选择一个不同的值。如果允许重复值，请使用 UNION ALL。

### SQL UNION 语法2

```
SELECT column_name(s) FROM table1
[WHERE condition]

UNION
SELECT column_name(s) FROM table2
[WHERE condition];
```

　给定的条件可以是基于您的需求的任何给定表达式。            
        

### SQL UNION ALL 语法1

　UNION All运算符用于组合两个SELECT语句(包括重复行)的结果。

　适用于UNION子句的相同规则将适用于UNION All操作符。

```
SELECT column_name(s) FROM table1
UNION ALL
SELECT column_name(s) FROM table2;
```

> **注释：**UNION结果集中的列名总是等于UNION中第一个SELECT语句中的列名。

### SQL UNION ALL 语法2

```
SELECT column_name(s) FROM table1
[WHERE condition]
UNION ALL
SELECT column_name(s) FROM table2
[WHERE condition];
```

​            
​        

## 演示数据库

------

　在本教程中，我们将使用著名的Northwind示例数据库。

　以下是"Customers" 表中的数据：

| CustomerID | CustomerName                       | ContactName    | Address                       | City        | PostalCode | Country |
| ---------- | ---------------------------------- | -------------- | ----------------------------- | ----------- | ---------- | ------- |
| 1          | Alfreds Futterkiste                | Maria Anders   | Obere Str. 57                 | Berlin      | 12209      | Germany |
| 2          | Ana Trujillo Emparedados y helados | Ana Trujillo   | Avda. de la Constitución 2222 | México D.F. | 05021      | Mexico  |
| 3          | Antonio Moreno Taquería            | Antonio Moreno | Mataderos 2312                | México D.F. | 05023      | Mexico  |

　选自 "Suppliers" 表的数据：

| SupplierID | SupplierName               | ContactName      | Address        | City        | PostalCode | Country |
| ---------- | -------------------------- | ---------------- | -------------- | ----------- | ---------- | ------- |
| 1          | Exotic Liquid              | Charlotte Cooper | 49 Gilbert St. | Londona     | EC1 4SD    | UK      |
| 2          | New Orleans Cajun Delights | Shelley Burke    | P.O. Box 78934 | New Orleans | 70117      | USA     |
| 3          | Grandma Kelly's Homestead  | Regina Murphy    | 707 Oxford Rd. | Ann Arbor   | 48104      | USA     |



------

## SQL UNION 实例

　下面的 SQL 语句从 "Customers" 和 "Suppliers" 表中选取所有**不同的**城市（只有不同的值）：

## 实例
```
SELECT City FROM Customers UNION  SELECT City FROM Suppliers  ORDER BY City;
```
> **注释：**不能用 UNION 来列出两个表中的所有城市。如果一些客户和供应商来自同一个城市，每个城市将只被列入一个列表。UNION将只选择不同的值。请使用UNION ALL选择重复值!



## SQL UNION ALL 实例

------

　以下SQL语句使用 UNION ALL 从 "Customers"和"Suppliers" 表中选择所有城市（也是重复的值）：            
        

## 实例
```
SELECT City FROM Customers UNION ALL SELECT City FROM Suppliers ORDER BY City;
```


## 带有 WHERE 的 SQL UNION ALL

------

　以下SQL语句使用UNIONALL从"Customers"和 "Suppliers" 表中选择所有德国城市（也是重复数值）：

## 实例

SELECT City, Country FROM Customers WHERE Country='Germany' UNION ALL SELECT City, Country FROM Suppliers WHERE Country='Germany' ORDER BY City;



## SQL UNION与WHERE

------

　以下SQL语句从“客户”和“供应商”中选择所有不同的德国城市（只有不同的值）：

```
SELECT City, Country FROM Customers WHERE Country='Germany' UNION SELECT City, Country FROM Suppliers WHERE Country='Germany' ORDER BY City;
```



## 另一个UNION示例

------

　以下SQL语句列出了所有客户和供应商：    

```
SELECT 'Customer' As Type, ContactName, City, Country FROM Customers UNION SELECT 'Supplier', ContactName, City, Country FROM Suppliers;
```

　还有另外两个子句(即运算符)，它们类似于UNION子句：

- **SQL INTERSECT子句**         
    用于组合两个SELECT语句，但只返回与第二个SELECT语句中的一行相同的第一个SELECT语句中的行。        
        
- **SQL EXCEPT子句**                
    用于组合两个SELECT语句，并返回第一个SELECT语句中没有由第二个SELECT语句返回的行。        
