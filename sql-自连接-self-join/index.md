# SQL-自连接 Self JOIN



## SQL自连接

------

　自联接是一种常规联接，但表本身是连接的。    

### Self JOIN语法

```
SELECT column_name(s)
FROM table1 T1, table1 T2
WHERE condition;
```



## 演示数据库

------

　在本教程中，我们将使用着名的Northwind示例数据库。

　以下是"Customers"表中的选择：

| CustomerID | CustomerName                       | ContactName    | Address                       | City        | PostalCode | Country |
| ---------- | ---------------------------------- | -------------- | ----------------------------- | ----------- | ---------- | ------- |
| 1          | Alfreds Futterkiste                | Maria Anders   | Obere Str. 57                 | Berlin      | 12209      | Germany |
| 2          | Ana Trujillo Emparedados y helados | Ana Trujillo   | Avda. de la Constitución 2222 | México D.F. | 05021      | Mexico  |
| 3          | Antonio Moreno Taquería            | Antonio Moreno | Mataderos 2312                | México D.F. | 05023      | Mexico  |



## SQL Self JOIN示例

------

　以下SQL语句匹配来自同一城市的客户：

### 代码示例

```
SELECT A.CustomerName AS CustomerName1, B.CustomerName AS CustomerName2, A.City FROM Customers A, Customers B WHERE A.CustomerID <> B.CustomerID AND A.City = B.City  ORDER BY A.City;
```
