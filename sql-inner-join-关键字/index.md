# SQL- INNER JOIN 关键字



## SQL INNER JOIN 关键字（内部连接）

------

　内部链接INNER JOIN关键字选择两个表中具有匹配值的记录。

### SQL INNER JOIN 语法

```
SELECT column_name(s) FROM table1 INNER JOIN table2 ON table1.column_name = table2.column_name;
```



　**注释：**INNER JOIN 与 JOIN 是相同的。

​    ![SQL INNER JOIN](https://atts.w3cschool.cn/attachments/day_160815/201608151946162952.gif)



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



## SQL INNER JOIN 实例

------

　以下SQL语句将返回所有下订单的客户：

## 实例
```
SELECT Customers.CustomerName, Orders.OrderID FROM Customers INNER JOIN Orders ON Customers.CustomerID=Orders.CustomerID ORDER BY Customers.CustomerName;
```
> **注释：**如果表中至少有一个匹配项，INNER JOIN 关键字将返回一行。如果 "Customers" 表中的行与"Orders" 不匹配，则不会列出行。



## 加入三张表

------

　以下SQL语句选择包含客户和货运单信息的所有订单：    

　**代码示例：**

```
SELECT Orders.OrderID, Customers.CustomerName, Shippers.ShipperName FROM ((Orders INNER JOIN Customers ON Orders.CustomerID = Customers.CustomerID) INNER JOIN Shippers ON Orders.ShipperID = Shippers.ShipperID);
```
