# SQL-IN 运算符



## SQL IN 运算符

------

　IN运算符允许您在WHERE子句中指定多个值。

　IN运算符是多个OR条件的简写。

### SQL IN 语法

```
SELECT column_name(s)
FROM table_name
WHERE column_name IN (value1, value2, ...);
```

### 或者

```
SELECT column_name(s)
FROM table_name
WHERE column_name IN (SELECT STATEMENT);
```



## 演示数据库

------

　在本教程中，我们将使用著名的Northwind示例数据库。

　以下数据选取自"Customers" 表：

| CustomerID | CustomerName                       | ContactName        | Address                       | City        | PostalCode | Country |
| ---------- | ---------------------------------- | ------------------ | ----------------------------- | ----------- | ---------- | ------- |
| 1          | Alfreds Futterkiste                | Maria Anders       | Obere Str. 57                 | Berlin      | 12209      | Germany |
| 2          | Ana Trujillo Emparedados y helados | Ana Trujillo       | Avda. de la Constitución 2222 | México D.F. | 05021      | Mexico  |
| 3          | Antonio Moreno Taquería            | Antonio Moreno     | Mataderos 2312                | México D.F. | 05023      | Mexico  |
| 4          | Around the Horn                    | Thomas Hardy       | 120 Hanover Sq.               | London      | WA1 1DP    | UK      |
| 5          | Berglunds snabbköp                 | Christina Berglund | Berguvsvägen 8                | Luleå       | S-958 22   | Sweden  |



## IN 操作符实例

------

　以下SQL语句选取位于“Germany”，“France”和“UK”的所有客户：

### 代码示例：

```
SELECT * FROM Customers
WHERE Country IN ('Germany', 'France', 'UK');
```

　以下SQL语句选取不在“Germany”，“France”或“UK”中的所有客户：

### 代码示例：

```
SELECT * FROM Customers
WHERE Country NOT IN ('Germany', 'France', 'UK');
```

　以下SQL语句选取来自同一国家的所有客户作为供应商：

### 代码示例：

```
SELECT * FROM Customers
WHERE Country IN (SELECT Country FROM Suppliers);
```
