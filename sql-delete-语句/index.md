# SQL-Delete 语句




## SQL DELETE 语句 

------

　DELETE语句用于删除表中现有记录。

### SQL DELETE 语法

```
DELETE FROM table_name
WHERE condition;
```



| ![lamp](https://atts.w3cschool.cn/attachments/image/20150510/lamp.jpg) | **请注意 ****删除表格中的记录时要小心！**** 注意SQL DELETE 语句中的 WHERE 子句！**                  WHERE子句指定需要删除哪些记录。如果省略了WHERE子句，表中所有记录都将被删除！ |
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



## SQL DELETE 实例

------

　假设我们想从"Customers" 表中删除客户“Alfreds Futterkiste”。

　我们使用以下SQL语句：

## 实例

DELETE FROM Customers        
WHERE CustomerName='Alfreds Futterkiste';        
    

　现在，"Customers" 表如下所示：

| CustomerID | CustomerName                       | ContactName        | Address                       | City        | PostalCode | Country |
| ---------- | ---------------------------------- | ------------------ | ----------------------------- | ----------- | ---------- | ------- |
| 2          | Ana Trujillo Emparedados y helados | Ana Trujillo       | Avda. de la Constitución 2222 | México D.F. | 05021      | Mexico  |
| 3          | Antonio Moreno Taquería            | Antonio Moreno     | Mataderos 2312                | México D.F. | 05023      | Mexico  |
| 4          | Around the Horn                    | Thomas Hardy       | 120 Hanover Sq.               | London      | WA1 1DP    | UK      |
| 5          | Berglunds snabbköp                 | Christina Berglund | Berguvsvägen 8                | Luleå       | S-958 22   | Sweden  |



## 删除所有数据

------

　您可以删除表中的所有行，而不需要删除该表。这意味着表的结构、属性和索引将保持不变：

```
DELETE FROM table_name;
```

　**或者**

```
DELETE * FROM table_name;
```

> **注意：**在没有备份的情况下，删除记录要格外小心！因为你删除了不能重复！

​    
