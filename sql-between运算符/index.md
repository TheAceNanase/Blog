# SQL-BETWEEN运算符



## SQL BETWEEN 运算符 

------

　BETWEEN运算符用于选取介于两个值之间的数据范围内的值。

　BETWEEN运算符选择给定范围内的值。值可以是数字，文本或日期。

　BETWEEN运算符是包含性的：包括开始和结束值，且开始值需小于结束值。 

### SQL BETWEEN 语法

```
SELECT column_name(s) FROM table_name WHERE column_name BETWEEN value1 AND value2;
```

​    

　要否定BETWEEN运算符的结果，可以添加NOT运算符：

```
SELECT column_name(s) FROM table_name WHERE column_name NOT BETWEEN value1 AND value2;
```



## 演示数据库

------

　在本教程中，我们将使用著名的Northwind示例数据库。

　以下是"Products"表中的数据：

| ProductID | ProductName                  | SupplierID | CategoryID | Unit                | Price |
| --------- | ---------------------------- | ---------- | ---------- | ------------------- | ----- |
| 1         | Chais                        | 1          | 1          | 10 boxes x 20 bags  | 18    |
| 2         | Chang                        | 1          | 1          | 24 - 12 oz bottles  | 19    |
| 3         | Aniseed Syrup                | 1          | 2          | 12 - 550 ml bottles | 10    |
| 4         | Chef Anton's Cajun Seasoning | 1          | 2          | 48 - 6 oz jars      | 22    |
| 5         | Chef Anton's Gumbo Mix       | 1          | 2          | 36 boxes            | 21.35 |



## BETWEEN 运算符实例

------

　以下SQL语句选择价格在10到20之间的所有产品：

## 实例
```
SELECT * FROM Products WHERE Price BETWEEN 10 AND 20;        
```



------

## NOT BETWEEN 操作符实例

　要显示前面示例范围之外的产品，请使用NOT BETWEEN：    

## 实例
```
SELECT * FROM Products RE Price NOT BETWEEN 10 AND 20;        
```



## 带有 IN 的 BETWEEN 操作符实例

------

　以下SQL语句选择价格在10到20之间但CategoryID不是1、2或3的所有产品：

## 实例
```
SELECT * FROM Products HERE (Price BETWEEN 10 AND 20) AND NOT CategoryID IN (1,2,3);
```



## 带有文本值的 BETWEEN 操作符实例

------

　以下SQL语句选择所有带有ProductName BETWEEN'Carnarvon Tigers'和'Mozzarella di Giovanni'的产品：    

## 实例
```
SELECT * FROM Products WHERE ProductName BETWEEN 'Carnarvon Tigers' AND 'Mozzarella di Giovanni' ORDER BY ProductName;
```



## 带有文本值的 NOT BETWEEN 操作符实例

------

　以下SQL语句选择ProductName不是BETWEEN'Carnarvon Tigers'和'Mozzarella di Giovanni'的所有产品：    

## 实例
```
SELECT * FROM Products WHERE ProductName NOT BETWEEN 'Carnarvon Tigers' AND 'Mozzarella di Giovanni' ORDER BY ProductName;
```



## 示例表

------

　下面是选自 "Orders" 表的数据：

| OrderID | CustomerID | EmployeeID | OrderDate | ShipperID |
| ------- | ---------- | ---------- | --------- | --------- |
| 10248   | 90         | 5          | 7/4/1996  | 3         |
| 10249   | 81         | 6          | 7/5/1996  | 1         |
| 10250   | 34         | 4          | 7/8/1996  | 2         |
| 10251   | 84         | 3          | 7/9/1996  | 1         |
| 10252   | 76         | 4          | 7/10/1996 | 2         |



## 带有日期值的 BETWEEN 操作符实例

------

　以下 SQL 语句选取 OrderDate 介于 '04-July-1996' 和 '09-July-1996' 之间的所有订单：

## 实例
```
SELECT * FROM Orders WHERE OrderDate BETWEEN #07/04/1996# AND #07/09/1996#;  ``` 
    



| ![lamp](https://atts.w3cschool.cn/attachments/image/20150510/lamp.jpg) | **请注意，在不同的数据库中，BETWEEN 操作符会产生不同的结果！ **在一些数据库中，BETWEEN 选取介于两个值之间但不包括两个测试值的字段。                     在一些数据库中，BETWEEN 选取介于两个值之间且包括两个测试值的字段。                     在一些数据库中，BETWEEN 选取介于两个值之间且包括第一个测试值但不包括最后一个测试值的字段。                **因此，请检查您的数据库是如何处理 BETWEEN 操作符！** |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |
