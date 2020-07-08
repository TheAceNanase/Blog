# SQL-Aliases 别名


## SQL 别名（Aliases） 

------

　通过使用 SQL，可以为表名称或列名称指定别名（Alias）。

- SQL 别名用于为表或表中的列提供临时名称，数据库中的实际表名不会更改。        
        
- SQL 别名通常用于使列名更具可读性。        
        
- SQL 一个别名只存在于查询期间。
- 表别名的使用是在特定SQL语句中重命名表。        
        
- 列别名用于为特定SQL查询重命名表的列。        
        

### 列的 SQL Alias 语法

```
SELECT column_name AS alias_name
FROM table_name;
WHERE [condition];
```

### 表的 SQL Alias 语法

```
SELECT column_name(s)
FROM table_name AS alias_name;
WHERE [condition];
```



## 演示数据库

------

　在本教程中，我们将使用著名的Northwind示例数据库。

　以下是"Customers" 表中的数据：

| CustomerID | CustomerName                       | ContactName    | Address                       | City        | PostalCode | Country |
| ---------- | ---------------------------------- | -------------- | ----------------------------- | ----------- | ---------- | ------- |
| 2          | Ana Trujillo Emparedados y helados | Ana Trujillo   | Avda. de la Constitución 2222 | México D.F. | 05021      | Mexico  |
| 3          | Antonio Moreno Taquería            | Antonio Moreno | Mataderos 2312                | México D.F. | 05023      | Mexico  |
| 4          | Around the Horn                    | Thomas Hardy   | 120 Hanover Sq.               | London      | WA1 1DP    | UK      |

　下面是选自 "Orders" 表的数据：

| OrderID | CustomerID | EmployeeID | OrderDate  | ShipperID |
| ------- | ---------- | ---------- | ---------- | --------- |
| 10354   | 58         | 8          | 1996-11-14 | 3         |
| 10355   | 4          | 6          | 1996-11-15 | 1         |
| 10356   | 86         | 6          | 1996-11-18 | 2         |



## 列的 Alias 实例

------

　以下SQL语句创建两个别名，一个用于CustomerID列，另一个用于CustomerName列：

## 实例

SELECT CustomerID as ID, CustomerName AS Customer        
FROM Customers;        
    

　以下SQL语句创建两个别名，一个用于CustomerName列，一个用于ContactName列。**注：**如果别名包含空格，则需要双引号或方括号：    

## 实例

SELECT CustomerName AS Customer, ContactName AS [Contact Person]        
FROM Customers;        
    

　以下SQL语句创建一个名为“Address”的别名，它包含四列（Address，PostalCode，City and Country）：

```
SELECT CustomerName, Address + ', ' + PostalCode + ' ' + City + ', ' + Country AS Address
FROM Customers;
```

　**注意：**要使上面的SQL语句在MySQL中工作，请使用以下命令：    

```
SELECT CustomerName, CONCAT(Address,', ',PostalCode,', ',City,', ',Country) AS Address
FROM Customers;
```



## 表的 Alias 实例

------

　以下SQL语句选择CustomerID = 4（“围绕角”）的所有订单。我们使用“Customers”和“Orders”表，给它们分别为“c”和“o”的表别名（这里我们使用别名来使SQL更短）：    

## 实例

SELECT o.OrderID, o.OrderDate, c.CustomerName        
FROM Customers AS c, Orders AS o        
WHERE c.CustomerName="Around the Horn" AND c.CustomerID=o.CustomerID;        
    

　以下SQL语句与上述相同，但没有别名：    

## 实例

SELECT Orders.OrderID, Orders.OrderDate, Customers.CustomerName        
FROM Customers, Orders        
WHERE Customers.CustomerName="Around the Horn" AND Customers.CustomerID=Orders.CustomerID;        
    

　在下列情况下使用别名是有用的：    

- 查询涉及多个表            
            
- 用于查询函数
- 需要把两个或更多的列放在一起
- 列名长或可读性差



## 示例

------

　考虑下面两个数据表：

　（a）CUSTOMERS 表，如下：

```
+----+----------+-----+-----------+----------+
| ID | NAME     | AGE | ADDRESS   | SALARY   |
+----+----------+-----+-----------+----------+
|  1 | Ramesh   |  32 | Ahmedabad |  2000.00 |
|  2 | Khilan   |  25 | Delhi     |  1500.00 |
|  3 | kaushik  |  23 | Kota      |  2000.00 |
|  4 | Chaitali |  25 | Mumbai    |  6500.00 |
|  5 | Hardik   |  27 | Bhopal    |  8500.00 |
|  6 | Komal    |  22 | MP        |  4500.00 |
|  7 | Muffy    |  24 | Indore    | 10000.00 |
+----+----------+-----+-----------+----------+
```

　（b）另一个是 ORDERS 表，如下所示：

```
+-----+---------------------+-------------+--------+
|OID  | DATE                | CUSTOMER_ID | AMOUNT |
+-----+---------------------+-------------+--------+
| 102 | 2009-10-08 00:00:00 |           3 |   3000 |
| 100 | 2009-10-08 00:00:00 |           3 |   1500 |
| 101 | 2009-11-20 00:00:00 |           2 |   1560 |
| 103 | 2008-05-20 00:00:00 |           4 |   2060 |
+-----+---------------------+-------------+--------+
```

　下面是**表别名**的用法：

```
SQL> SELECT C.ID, C.NAME, C.AGE, O.AMOUNT 
        FROM CUSTOMERS AS C, ORDERS AS O
        WHERE  C.ID = O.CUSTOMER_ID;
```

　上面语句的运行结果如下所示：

```
+----+----------+-----+--------+
| ID | NAME     | AGE | AMOUNT |
+----+----------+-----+--------+
|  3 | kaushik  |  23 |   3000 |
|  3 | kaushik  |  23 |   1500 |
|  2 | Khilan   |  25 |   1560 |
|  4 | Chaitali |  25 |   2060 |
+----+----------+-----+--------+
```

　下面是**列别名**的用法：

```
SQL> SELECT  ID AS CUSTOMER_ID, NAME AS CUSTOMER_NAME
     FROM CUSTOMERS
     WHERE SALARY IS NOT NULL;
```

　其运行结果如下所示：

```
+-------------+---------------+
| CUSTOMER_ID | CUSTOMER_NAME |
+-------------+---------------+
|           1 | Ramesh        |
|           2 | Khilan        |
|           3 | kaushik       |
|           4 | Chaitali      |
|           5 | Hardik        |
|           6 | Komal         |
|           7 | Muffy         |
+-------------+---------------+
```
