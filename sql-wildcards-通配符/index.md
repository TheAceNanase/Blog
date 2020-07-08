# SQL-Wildcards 通配符



## SQL Wildcards通配符

------

　通配符用于替换字符串中的任何其他字符。            
        

　通配符与[SQL LIKE](https://www.w3cschool.cn/sql/djarwfop.html)运算符一起使用。在WHERE子句中使用LIKE运算符来搜索列中的指定模式。 

　有两个通配符与LIKE运算符一起使用：

- `％` - 百分号表示零个，一个或多个字符
- `_` - 下划线表示单个字符

　**注意：** 



- MS Access使用星号(`*`）通配符而不是百分比符号(`%`)通配符。                 
                
- MS Access使用问号（`?`）而不是下划线（`_`）。                
                



　在MS Access和SQL Server中，你也可以使用：

- [ charlist ] - 定义要匹配的字符的集合和范围
- [^ charlist ]或[！charlist ] - 定义不匹配字符的集合和范围

　通配符也可以组合使用！

　下面是一些使用'`％`'和'`_`'通配符显示不同LIKE运算符的例子：

| LIKE运算符                      | 描述                                   |
| ------------------------------- | -------------------------------------- |
| WHERE CustomerName LIKE 'a%'    | 查找以“a”开头的任何值                  |
| WHERE CustomerName LIKE '%a'    | 查找以"a"结尾的任何值                  |
| WHERE CustomerName LIKE '%or%'  | 在任何位置查找任何具有“or”的值         |
| WHERE CustomerName LIKE '_r%'   | 在第二个位置查找任何具有“r”的值        |
| WHERE CustomerName LIKE 'a_%_%' | 查找以“a”开头并且长度至少为3个字符的值 |
| WHERE ContactName LIKE 'a%o'    | 查找以“a”开始并以“o”结尾的任何值       |



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



## 使用 SQL % 通配符

------

　以下SQL语句选择所有客户City以字母“ber”开头：

## 实例

SELECT * FROM Customers                
WHERE City LIKE 'ber%';

　以下SQL语句选择City中包含“es”模式的所有客户：

## 实例

SELECT * FROM Customers                
WHERE City LIKE '%es%';



## 使用 SQL _ 通配符

------

　以下SQL语句选择City以任意字符开头，然后是“erlin”的所有客户：

## 实例

SELECT * FROM Customers                
WHERE City LIKE '_erlin';

　以下SQL语句选择City开头为“L”，后面是任意字符，后面是“n”，后面是任意字符，后面是“on”的所有客户：

## 实例

SELECT * FROM Customers                
WHERE City LIKE 'L_n_on';



## 使用 SQL [charlist] 通配符

------

　以下SQL语句选择所有客户City以"b"、"s"或"p"开头：

## 实例

SELECT * FROM Customers                
WHERE City LIKE '[bsp]%';

　以下SQL语句选择“City”以“a”、“b”或“c”开头的所有客户：

## 实例

SELECT * FROM Customers                
WHERE City LIKE '[a-c]%';

　以下SQL语句选择所有客户City不以"b"、"s"或"p"开头：

## 实例

SELECT * FROM Customers                
WHERE City LIKE '[!bsp]%';



## 使用[！charlist]通配符

------

　以下两个SQL语句选择所有客户的城市不以“b”，“s”或“p”开头：

　**代码示例：**

```
SELECT * FROM Customers
WHERE City LIKE '[!bsp]%';
```

　要么：

　**代码示例：**

```
SELECT * FROM Customers
WHERE City NOT LIKE '[bsp]%';
```

​    
