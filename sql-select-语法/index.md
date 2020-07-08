# SQL-SELECT语法

## SQL SELECT 语法

SELECT 语法用于从数据库中选择数据。 
    
返回的数据存储在结果表中，称为结果集。
    
### 基本语法：SELECT和FROM

在任何SQL查询语句中都：SELECT和FROM他们必须按顺序排列。SELECT指示要查看哪些列，FROM标识它们所在的表。

SQL SELECT 语法如下所示：
    
```
SELECT column1, column2, ...
FROM table_name;
```

这里，column1，column2，...是要从中选择数据的表的字段名称。如果要选择表中可用的所有字段，请使用以下语法：
    
```
SELECT * FROM table_name;
```

## 演示数据库

下面是罗斯文示例数据库中“Customers”表的一个选择：
    
| CustomerID | CustomerName | ContactName | Address | City | PostalCode | Country |
| - | - | - | - | - | - | - |
| 1| Alfreds Futterkiste | Maria Anders | Obere Str. 57 | Berlin | 12209 | Germany |
| 2 | Ana Trujillo Emparedados y helados | Ana Trujillo | Avda. de la Constitución 2222 | México D.F. | 05021 | Mexico |
| 3 | Antonio Moreno Taquería | Antonio Moreno | Mataderos 2312 | México D.F. | 05023 | Mexico |
| 4 | Around the Horn | Thomas Hardy | 120 Hanover Sq. | London | WA1 1DP | UK |
| 5 | Berglunds snabbköp | Christina Berglund | Berguvsvägen 8 | Luleå | S-958 22 | Sweden |



## SELECT Column 实例
我们将为以下三种用例提供实例


> 1、检索一列  
>     
> 2、检索多列 
>     
> 3、检索所有列

用上述的“Customers”表来说明三种用例的使用。


### SELECT  检索一列

下面的 SQL 语句从 "Customers" 表中选取 "City" 列：

## 实例

SELECT City FROM Customers; 
        
### SELECT  检索多列

下面的 SQL 语句从 "Customers" 表中选取 "CustomerName" 和 "City" 列：

## 实例
 SELECT CustomerName, City FROM Customers;
        
>
>   注意：这两个列名在查询中用逗号分隔。每当您选择多个列时，它们必须用逗号分隔，但最后一列名称之后不能添加逗号。
>

### SELECT * 实例 - 检索所有列

下面的 SQL 语句从 "Customers" 表中选取所有列：

## 实例

SELECT * FROM Customers;
        
如果要选择表中的所有列，则可以使用 * 而不需要把所有列名罗列查询。

