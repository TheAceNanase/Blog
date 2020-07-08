# SQL-UPDATE 语句



## SQL UPDATE 语句

------

　UPDATE 语句用于更新表中已存在的记录。

　还可以使用AND或OR运算符组合多个条件。                    
        

### SQL UPDATE 语法

　具有WHERE子句的UPDATE查询的基本语法如下所示：            
        

```
UPDATE table_name
SET column1 = value1, column2 = value2, ...
WHERE condition;
```



| ![lamp](https://atts.w3cschool.cn/attachments/image/20150510/lamp.jpg) | **请注意  ****更新表中的记录时要小心！**** 要注意SQL UPDATE 语句中的 WHERE 子句！**                          WHERE子句指定哪些记录需要更新。如果省略WHERE子句，所有记录都将更新！ |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |



## 演示数据库

------

　在本教程中，我们将使用著名的Northwind示例数据库。

　以下是 "Customers" 表中的数据：

| CustomerID | CustomerName                       | ContactName        | Address                       | City        | PostalCode | Country |
| ---------- | ---------------------------------- | ------------------ | ----------------------------- | ----------- | ---------- | ------- |
| 1          | Alfreds Futterkiste                | Maria Anders       | Obere Str. 57                 | Berlin      | 12209      | Germany |
| 2          | Ana Trujillo Emparedados y helados | Ana Trujillo       | Avda. de la Constitución 2222 | México D.F. | 05021      | Mexico  |
| 3          | Antonio Moreno Taquería            | Antonio Moreno     | Mataderos 2312                | México D.F. | 05023      | Mexico  |
| 4          | Around the Horn                    | Thomas Hardy       | 120 Hanover Sq.               | London      | WA1 1DP    | UK      |
| 5          | Berglunds snabbköp                 | Christina Berglund | Berguvsvägen 8                | Luleå       | S-958 22   | Sweden  |



## SQL UPDATE 实例

------

　以下SQL语句为第一个客户（CustomerID = 1）更新了“CustomerName”和“City”：

## 实例

UPDATE Customers                
SET ContactName = 'Alfred Schmidt', City= 'Frankfurt'                
WHERE CustomerID = 1;                
            

　现在，选自 "Customers" 表的数据如下所示：

| CustomerID | CustomerName                       | ContactName        | Address                       | City        | PostalCode | Country |
| ---------- | ---------------------------------- | ------------------ | ----------------------------- | ----------- | ---------- | ------- |
| 1          | Alfreds Futterkiste                | Alfred Schmidt     | Obere Str. 57                 | Frankfurt   | 12209      | Germany |
| 2          | Ana Trujillo Emparedados y helados | Ana Trujillo       | Avda. de la Constitución 2222 | México D.F. | 05021      | Mexico  |
| 3          | Antonio Moreno Taquería            | Antonio Moreno     | Mataderos 2312                | México D.F. | 05023      | Mexico  |
| 4          | Around the Horn                    | Thomas Hardy       | 120 Hanover Sq.               | London      | WA1 1DP    | UK      |
| 5          | Berglunds snabbköp                 | Christina Berglund | Berguvsvägen 8                | Luleå       | S-958 22   | Sweden  |



## 更新多个记录

------

　WHERE子句决定了将要更新的记录数量。

　以下SQL语句将把国家/地区为"Mexico"的所有记录的联系人姓名更新为“Juan”：

```
UPDATE Customers
SET ContactName='Juan'
WHERE Country='Mexico';
```

　“Customers”表中的选择现在看起来像这样：            
        

| CustomerID | CustomerName                       | ContactName        | Address                       | City        | PostalCode | Country |
| ---------- | ---------------------------------- | ------------------ | ----------------------------- | ----------- | ---------- | ------- |
| 1          | Alfreds Futterkiste                | Alfred Schmidt     | Obere Str. 57                 | Frankfurt   | 12209      | Germany |
| 2          | Ana Trujillo Emparedados y helados | Juan               | Avda. de la Constitución 2222 | México D.F. | 05021      | Mexico  |
| 3          | Antonio Moreno Taquería            | Juan               | Mataderos 2312                | México D.F. | 05023      | Mexico  |
| 4          | Around the Horn                    | Thomas Hardy       | 120 Hanover Sq.               | London      | WA1 1DP    | UK      |
| 5          | Berglunds snabbköp                 | Christina Berglund | Berguvsvägen 8                | Luleå       | S-958 22   | Sweden  |

​            
​        

## Update 警告！

------

　更新记录时要小心。如果您省略WHERE子句，所有记录将被更新！            
        

```
UPDATE Customers
SET ContactName='Juan';
```

​            
​        

"Customers" 表将如下所示：

| CustomerID | CustomerName                       | ContactName | Address                       | City        | PostalCode | Country |
| ---------- | ---------------------------------- | ----------- | ----------------------------- | ----------- | ---------- | ------- |
| 1          | Alfreds Futterkiste                | Juan        | Obere Str. 57                 | Frankfurt   | 12209      | Germany |
| 2          | Ana Trujillo Emparedados y helados | Juan        | Avda. de la Constitución 2222 | México D.F. | 05021      | Mexico  |
| 3          | Antonio Moreno Taquería            | Juan        | Mataderos 2312                | México D.F. | 05023      | Mexico  |
| 4          | Around the Horn                    | Juan        | 120 Hanover Sq.               | London      | WA1 1DP    | UK      |
| 5          | Berglunds snabbköp                 | Juan        | Berguvsvägen 8                | Luleå       | S-958 22   | Sweden  |

