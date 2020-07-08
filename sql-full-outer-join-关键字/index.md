# SQL-FULL OUTER JOIN 关键字



## SQL FULL OUTER JOIN 关键字 

------

　当左（表1）或右（表2）表记录匹配时，FULL OUTER JOIN关键字将返回所有记录。    

　**注意：** FULL OUTER JOIN可能会返回非常大的结果集！    

### SQL FULL OUTER JOIN 语法

```
SELECT column_name(s) FROM table1 FULL OUTER JOIN table2 ON table1.column_name = table2.column_name;
```

​    ![SQL FULL OUTER JOIN](https://atts.w3cschool.cn/attachments/day_160815/201608151947092720.gif)    



## 演示数据库

------

　在本教程中，我们将使用著名的Northwind示例数据库。

　以下是"Customers" 表中的数据：

| CustomerID | CustomerName                       | ContactName    | Address                       | City        | PostalCode | Country |
| ---------- | ---------------------------------- | -------------- | ----------------------------- | ----------- | ---------- | ------- |
| 1          | Alfreds Futterkiste                | Maria Anders   | Obere Str. 57                 | Berlin      | 12209      | Germany |
| 2          | Ana Trujillo Emparedados y helados | Ana Trujillo   | Avda. de la Constitución 2222 | México D.F. | 05021      | Mexico  |
| 3          | Antonio Moreno Taquería            | Antonio Moreno | Mataderos 2312                | México D.F. | 05023      | Mexico  |

　选自 "Orders" 表的数据：

| OrderID | CustomerID | EmployeeID | OrderDate  | ShipperID |
| ------- | ---------- | ---------- | ---------- | --------- |
| 10308   | 2          | 7          | 1996-09-18 | 3         |
| 10309   | 3          | 3          | 1996-09-19 | 1         |
| 10310   | 77         | 8          | 1996-09-20 | 2         |



## SQL FULL OUTER JOIN 实例

------

　以下SQL语句选择所有客户和所有订单：

```
SELECT Customers.CustomerName, Orders.OrderID FROM Customers FULL OUTER JOIN Orders ON Customers.CustomerID=Orders.CustomerID ORDER BY Customers.CustomerName;
```

　从这套结果中选择的数据如下：

| CustomerName                       | OrderID |
| ---------------------------------- | ------- |
| Alfreds Futterkiste                |         |
| Ana Trujillo Emparedados y helados | 10308   |
| Antonio Moreno Taquería            | 10365   |
|                                    | 10382   |
|                                    | 10351   |

> **注意：** FULL OUTER  JOIN关键字返回左表（Customers）中的所有行，以及右表（Orders）中的所有行。如果 "Customers"中的行中没有"Orders"中的匹配项，或者"Orders"中的行中没有 "Customers"中的匹配项，那么这些行也会列出。

​    
