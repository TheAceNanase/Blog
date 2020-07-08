# SQL-SELECT INTO 语句



## SQL SELECT INTO 语句

------

　使用 SQL，您可以将信息从一个表中复制到另一个表中。

　SELECT INTO 语句从一个表中复制数据，然后将数据插入到另一个新表中。

### SQL SELECT INTO 语法

　我们可以把所有的列都复制到新表中：

```
SELECT * INTO newtable [IN externaldb] FROM table1;
```

　或者只复制希望的列插入到新表中：

```
SELECT column_name(s) INTO newtable [IN externaldb] FROM table1;
```



| ![lamp](https://atts.w3cschool.cn/attachments/image/20150510/lamp.jpg) | **提示：**将使用SELECT语句中定义的列名和类型创建新表。您可以使用AS子句来应用一个新名称。 |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |



## SQL SELECT INTO 实例

------

　创建 Customers 的备份复件：

```
SELECT * INTO CustomersBackup2013 FROM Customers;
```

　请使用 IN 子句来复制表到另一个数据库中：

```
SELECT * INTO CustomersBackup2013 IN 'Backup.mdb' FROM Customers;
```

　只复制一些列插入到新表中：

```
SELECT CustomerName, ContactName INTO CustomersBackup2013 FROM Customers;
```

　只复制德国的客户插入到新表中：

```
SELECT * INTO CustomersBackup2013 FROM Customers WHERE Country='Germany';
```

　复制多个表中的数据插入到新表中：

```
SELECT Customers.CustomerName, Orders.OrderID INTO CustomersOrderBackup2013 FROM Customers LEFT JOIN Orders ON Customers.CustomerID=Orders.CustomerID;
```

　**提示：**SELECT INTO 语句可以用于在另一种模式下创建一个新的空表。只需添加WHERE子句，使查询返回时没有数据：

```
SELECT * INTO newtable FROM table1 WHERE 1=0;
```
