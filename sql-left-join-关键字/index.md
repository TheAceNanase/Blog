# SQL-LEFT JOIN 关键字



## SQL 左连接 LEFT JOIN 关键字

------

　SQL左链接LEFT JOIN关键字返回左表（表1）中的所有行，即使在右表（表2）中没有匹配。如果在正确的表中没有匹配，结果是NULL。

### SQL LEFT JOIN 语法

```
SELECT column_name(s) FROM table1 LEFT JOIN table2 ON table1.column_name=table2.column_name;       
```

　或：

```
SELECT column_name(s) FROM table1  LEFT OUTER JOIN table2 ON table1.column_name=table2.column_name;      
```

​        

> **注释：**在一些数据库中，LEFT JOIN称为LEFT OUT ER JOIN。

​            ![SQL LEFT JOIN](https://atts.w3cschool.cn/attachments/day_160815/201608151944346042.gif)        



## 演示数据库

------

　在本教程中，我们将使用著名的Northwind示例数据库。

　以下是 "Customers" 表中的数据：

| CustomerID | CustomerName                       | ContactName    | Address                       | City        | PostalCode | Country |
| ---------- | ---------------------------------- | -------------- | ----------------------------- | ----------- | ---------- | ------- |
| 1          | Alfreds Futterkiste                | Maria Anders   | Obere Str. 57                 | Berlin      | 12209      | Germany |
| 2          | Ana Trujillo Emparedados y helados | Ana Trujillo   | Avda. de la Constitución 2222 | México D.F. | 05021      | Mexico  |
| 3          | Antonio Moreno Taquería            | Antonio Moreno | Mataderos 2312                | México D.F. | 05023      | Mexico  |

　选自 "Orders" 表的数据：

| OrderID | CustomerID | EmployeeID | OrderDate  | ShipperID |
| ------- | ---------- | ---------- | ---------- | --------- |
| 10308   | 2          | 7          | 1996-09-18 | 3         |
| 10309   | 37         | 3          | 1996-09-19 | 1         |
| 10310   | 77         | 8          | 1996-09-20 | 2         |



## SQL LEFT JOIN 实例

------

　以下SQL语句将选择所有客户以及他们可能拥有的任何订单：            
        

## 实例
```
SELECT Customers.CustomerName, Orders.OrderID FROM Customers LEFT JOIN Orders ON Customers.CustomerID = Orders.CustomerID ORDER BY Customers.CustomerName;  
```
> **注释：**LEFT JOIN 关键字返回左表（Customers）中的所有行，即使在右边表（Orders）中没有匹配。
