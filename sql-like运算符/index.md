# SQL-LIKE运算符



## SQL LIKE 运算符 

------

　在WHERE子句中使用LIKE运算符来搜索列中的指定模式。 

　有两个通配符与LIKE运算符一起使用：

- `％` - 百分号表示零个，一个或多个字符
- `_` - 下划线表示单个字符

　**注意：** MS Access使用问号（`?`）而不是下划线（`_`）。        
    

　百分号和下划线也可以组合使用！ 

### SQL LIKE 语法

```
SELECT column1, column2, ...
FROM table_name
WHERE columnN LIKE pattern;
```

　**提示**：您还可以使用AND或OR运算符组合任意数量的条件。    

　下面是一些使用'％'和'_'通配符显示不同LIKE运算符的例子：        
    

| LIKE 运算符                     | 描述                                 |
| ------------------------------- | ------------------------------------ |
| WHERE CustomerName LIKE 'a%'    | 查找以“a”开头的任何值                |
| WHERE CustomerName LIKE '%a'    | 查找以“a”结尾的任何值                |
| WHERE CustomerName LIKE '%or%'  | 在任何位置查找任何具有“or”的值       |
| WHERE CustomerName LIKE '_r%'   | 在第二个位置查找任何具有“r”的值      |
| WHERE CustomerName LIKE 'a_%_%' | 查找以“a”开头且长度至少为3个字符的值 |
| WHERE ContactName LIKE 'a%o'    | 找到以"a"开头，以"o"结尾的值         |



## 演示数据库

------

　在本教程中，我们将使用著名的Northwind示例数据库。

　以下是"Customers"表中的数据：

| CustomerID | CustomerName                       | ContactName        | Address                       | City        | PostalCode | Country |
| ---------- | ---------------------------------- | ------------------ | ----------------------------- | ----------- | ---------- | ------- |
| 1          | Alfreds Futterkiste                | Maria Anders       | Obere Str. 57                 | Berlin      | 12209      | Germany |
| 2          | Ana Trujillo Emparedados y helados | Ana Trujillo       | Avda. de la Constitución 2222 | México D.F. | 05021      | Mexico  |
| 3          | Antonio Moreno Taquería            | Antonio Moreno     | Mataderos 2312                | México D.F. | 05023      | Mexico  |
| 4          | Around the Horn                    | Thomas Hardy       | 120 Hanover Sq.               | London      | WA1 1DP    | UK      |
| 5          | Berglunds snabbköp                 | Christina Berglund | Berguvsvägen 8                | Luleå       | S-958 22   | Sweden  |



## SQL LIKE 运算符实例

------

　以下SQL语句选择以“a”开头的CustomerName的所有客户：        
    

　**代码示例：**     

```
SELECT * FROM Customers
WHERE CustomerName LIKE 'a%';
```

　以下SQL语句选择客户名称以“a”结尾的所有客户：

　**代码示例：**    

```
SELECT * FROM Customers
WHERE CustomerName LIKE '%a';
```

　以下SQL语句选择客户名称在任何位置都具有“或”的所有客户：

　**代码示例：**    

```
SELECT * FROM Customers
WHERE CustomerName LIKE '%or%';
```

　以下SQL语句选择客户名称在第二位具有“r”的所有客户：

　**代码示例：**    

```
SELECT * FROM Customers
WHERE CustomerName LIKE '_r%';
```

　以下SQL语句选择客户名称以“a”开头且长度至少为3个字符的所有客户：

　**代码示例：**    

```
SELECT * FROM Customers
WHERE CustomerName LIKE 'a_%_%';
```

　以下SQL语句选择联系人名称以“a”开头并以“o”结尾的所有客户：

　**代码示例：**    

```
SELECT * FROM Customers
WHERE ContactName LIKE 'a%o';
```

　以下SQL语句选择客户名称不以“a”开头的所有客户：

　**代码示例：**    

```
SELECT * FROM Customers
WHERE CustomerName NOT LIKE 'a%';
```

　以下SQL语句选择客户名称以“a”开头，以“s”结尾的5位字符的所有客户：

　**代码示例：**

```
SELECT * FROM Customers
WHERE CustomerName NOT LIKE 'a___s';
```


