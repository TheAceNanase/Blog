# SQL-ORDER BY 关键字


## SQL ORDER BY 关键字

ORDER BY 关键字用于按升序或降序对结果集进行排序。

ORDER BY 关键字默认情况下按升序排序记录。

如果需要按降序对记录进行排序，可以使用DESC关键字。

### SQL ORDER BY 语法

```
SELECT column1, column2, ...
FROM table_name
ORDER BY column1, column2, ... ASC|DESC;
```
您可以在ORDER BY子句中使用多个列，但要确保用于对该列进行排序的列应该在列表中。

## 演示数据库

以下是 "Customers" 表中的数据：


| CustomerID | CustomerName | ContactName | Address | City | PostalCode | Country |
| - | - | - | - | - | - | - |
| 1 | Alfreds Futterkiste | Maria Anders | Obere Str. 57 | Berlin | 12209 | Germany |
| 2 | Ana Trujillo Emparedados y helados | Ana Trujillo | Avda. de la Constitución 2222 | México D.F. | 05021 | Mexico |
| 3 | Antonio Moreno Taquería | Antonio Moreno | Mataderos 2312 | México D.F. | 05023 | Mexico |
| 4 | Around the Horn | Thomas Hardy | 120 Hanover Sq. | London | WA1 1DP | UK |
| 5 | Berglunds snabbköp | Christina Berglund | Berguvsvägen 8 | Luleå | S-958 22 | Sweden |


## ORDER BY 实例

下面的 SQL 语句从 "Customers" 表中选取所有客户，并按照 "Country" 列排序：


 ## 实例
```
SELECT * FROM Customers ORDER BY Country;
```
## ORDER BY DESC 实例

下面的 SQL 语句从 "Customers" 表中选取所有客户，并按照 "Country" 列降序排序：

 ## 实例
```
SELECT * FROM Customers ORDER BY Country DESC;
```
## ORDER BY 多列 实例1

下面的 SQL 语句从 "Customers" 表中选取所有客户，并按照 "Country" 和 "CustomerName" 列排序：
## 实例
```
SELECT * FROM Customers ORDER BY Country, CustomerName;
```        

   
## ORDER BY 多列 实例2
以下SQL语句从"Customers" 表中选择所有客户，按 "Country" 升序排列，并按 "CustomerName" 列降序排列：
    
```
SELECT * FROM Customers ORDER BY Country ASC, CustomerName DESC;
```

