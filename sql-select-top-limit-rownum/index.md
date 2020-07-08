# SQL-SELECT TOP, LIMIT, ROWNUM

# SQL SELECT TOP, LIMIT, ROWNUM

## SQL SELECT TOP 子句 

------

- SELECT TOP 子句用于指定要返回的记录数量。        
        
- SELECT TOP子句在包含数千条记录的大型表上很有用。返回大量记录会影响性能。        
        

> **注：**并不是所有的数据库系统都支持SELECT TOP子句。MySQL支持LIMIT子句来选择有限数量的记录，而Oracle使用ROWNUM。

### SQL Server / MS Access 语法

```
SELECT TOP number|percent column_name(s)
FROM table_name
WHERE condition;
```



## MySQL 和 Oracle 中的 SQL SELECT TOP 是等价的

------

### **MySQL语法：**

```
SELECT column_name(s)
FROM table_name
WHERE condition
LIMIT number;
```

#### 实例

```
SELECT *
FROM Persons
LIMIT 5;
```

### Oracle 语法

```
SELECT column_name(s)
FROM table_name
WHERE ROWNUM <= number;
```

#### 实例

```
SELECT *
FROM Persons
WHERE ROWNUM <=5;
```



## 演示数据库

------

　在本教程中，我们将使用著名的Northwind示例数据库。

　以下是"Customers" 表中的数据：

| CustomerID | CustomerName                       | ContactName        | Address                       | City        | PostalCode | Country |
| ---------- | ---------------------------------- | ------------------ | ----------------------------- | ----------- | ---------- | ------- |
| 1          | Alfreds Futterkiste                | Maria Anders       | Obere Str. 57                 | Berlin      | 12209      | Germany |
| 2          | Ana Trujillo Emparedados y helados | Ana Trujillo       | Avda. de la Constitución 2222 | México D.F. | 05021      | Mexico  |
| 3          | Antonio Moreno Taquería            | Antonio Moreno     | Mataderos 2312                | México D.F. | 05023      | Mexico  |
| 4          | Around the Horn                    | Thomas Hardy       | 120 Hanover Sq.               | London      | WA1 1DP    | UK      |
| 5          | Berglunds snabbköp                 | Christina Berglund | Berguvsvägen 8                | Luleå       | S-958 22   | Sweden  |



## SQL SELECT TOP 实例

------

　以下SQL语句从"Customers" 表中选择前两条记录：

## 实例

SELECT TOP 2 * FROM Customers;



## SQL SELECT TOP PERCENT 实例

------

　以下SQL语句从 "Customers" 表中选择前50%的记录：

## 实例

SELECT TOP 50 PERCENT * FROM Customers;



## SQL TOP，LIMIT和ROWNUM示例

------

　以下SQL语句从"Customers"表中选择前三个记录：    

```
SELECT TOP 3 * FROM Customers;
```

　以下SQL语句显示了使用LIMIT子句的等效示例：    

```
SELECT * FROM Customers
LIMIT 3;
```

　以下SQL语句显示了使用ROWNUM的等效示例：    

```
SELECT * FROM Customers
WHERE ROWNUM <= 3;
```



## SQL TOP PERCENT示例

------

　以下SQL语句从"Customers"表中选择记录的前50％：    

```
SELECT TOP 50 PERCENT * FROM Customers;
```



## 添加一个条件

------

　以下SQL语句从"Customers"表中选择国家为"Germany"的前三条记录：    

```
SELECT TOP 3 * FROM Customers
WHERE Country='Germany';
```

　以下SQL语句显示了使用LIMIT子句的等效示例：    

```
SELECT * FROM Customers
WHERE Country='Germany'
LIMIT 3;
```

　以下SQL语句显示了使用ROWNUM的等效示例：    

```
SELECT * FROM Customers
WHERE Country='Germany' AND ROWNUM <= 3;
```



## 为什么要LIMIT你的查询结果

------

　LIMIT作为一种简单的分页方法，主要是为了减少数据返回的时间，如果您查询一个非常大的表(例如一个有数十万或数百万行的表)而不使用限制，那么您可能会等待很长时间才能显示所有的结果，所以使用LIMIT可以减少查询数据返回的时间，提高效率。
