# SQL-RIGHT JOIN 关键字



## SQL右连接 RIGHT JOIN 关键字

------

　SQL右链接 RIGHT JOIN 关键字返回右表（table2）的所有行，即使在左表（table1）上没有匹配。如果左表没有匹配，则结果为NULL。

### SQL RIGHT JOIN 语法

```
SELECT column_name(s) FROM table1 RIGHT JOIN table2 ON table1.column_name = table2.column_name;
```

> **注释：**在一些数据库中，RIGHT JOIN 称为 RIGHT OUTER JOIN。

​            ![SQL RIGHT JOIN](https://atts.w3cschool.cn/attachments/day_160815/201608151944076968.gif)        



------

## 演示数据库

　在本教程中，我们将使用着名的Northwind示例数据库。

　以下是"Orders"表中的一个选项：

| OrderID | CustomerID | EmployeeID | OrderDate  | ShipperID |
| ------- | ---------- | ---------- | ---------- | --------- |
| 10308   | 2          | 7          | 1996-09-18 | 3         |
| 10309   | 37         | 3          | 1996-09-19 | 1         |
| 10310   | 77         | 8          | 1996-09-20 | 2         |

　并从"Employees" t表中选择：

| EmployeeID | LastName  | FirstName | BirthDate | Photo      |
| ---------- | --------- | --------- | --------- | ---------- |
| 1          | Davolio   | Nancy     | 12/8/1968 | EmpID1.pic |
| 2          | Fuller    | Andrew    | 2/19/1952 | EmpID2.pic |
| 3          | Leverling | Janet     | 8/30/1963 | EmpID3.pic |



## SQL RIGHT JOIN 实例

------

　以下SQL语句将返回所有雇员以及他们可能已经放置的任何订单：            
        

## 实例
```
SELECT Orders.OrderID, Employees.LastName, Employees.FirstName FROM Orders RIGHT JOIN Employees ON Orders.EmployeeID = Employees.EmployeeID ORDER BY Orders.OrderID;                
```

> **注释：**RIGHT JOIN 关键字返回右表（Employees）的所有行，即使在左表（Orders）中没有匹配。
