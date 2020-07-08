# SQL表达式



## SQL 表达式

------

　表达式是计算值的一个或多个值、运算符和SQL函数的组合。这些SQL表达式类似于公式，它们是用查询语言编写的。

　您还可以使用它们查询数据库中的特定数据集。

### 句法

　考虑SELECT语句的基本语法，如下所示：

```
SELECT column1, column2, columnN 
FROM table_name 
WHERE [CONDITION|EXPRESSION];
```

　有不同类型的sql表达式，如下所示：

- 布尔型
- 数值型
- 日期

　现在让我们详细讨论每一个问题。



## 布尔表达式

------

　SQL布尔表达式基于匹配单个值获取数据。

　句法：

```
SELECT column1, column2, columnN 
FROM table_name 
WHERE SINGLE VALUE MATCHING EXPRESSION;
```

　使用具有以下记录的Customers表：

```
SQL> SELECT * FROM CUSTOMERS;
+----+----------+-----+-----------+----------+
| ID | NAME     | AGE | ADDRESS   | SALARY   |
+----+----------+-----+-----------+----------+
|  1 | Ramesh   |  32 | Ahmedabad |  2000.00 |
|  2 | Khilan   |  25 | Delhi     |  1500.00 |
|  3 | kaushik  |  23 | Kota      |  2000.00 |
|  4 | Chaitali |  25 | Mumbai    |  6500.00 |
|  5 | Hardik   |  27 | Bhopal    |  8500.00 |
|  6 | Komal    |  22 | MP        |  4500.00 |
|  7 | Muffy    |  24 | Indore    | 10000.00 |
+----+----------+-----+-----------+----------+
7 rows in set (0.00 sec)
```

　下表是一个简单的示例，展示了各种sql布尔表达式的用法。

```
SQL> SELECT * FROM CUSTOMERS WHERE SALARY = 10000;
+----+-------+-----+---------+----------+
| ID | NAME  | AGE | ADDRESS | SALARY   |
+----+-------+-----+---------+----------+
|  7 | Muffy |  24 | Indore  | 10000.00 |
+----+-------+-----+---------+----------+
1 row in set (0.00 sec)
```



## 数值表达式

------

　数值表达式用于在任何查询中执行任何数学运算。

　句法：

```
SELECT numerical_expression as  OPERATION_NAME
[FROM table_name
WHERE CONDITION] ;
```

　这里，数值表达式用于数学表达式或任何公式。下面是一个简单的示例，展示了SQLNDigitic表达式的用法：

```
SQL> SELECT (15 + 6) AS ADDITION
+----------+
| ADDITION |
+----------+
|       21 |
+----------+
1 row in set (0.00 sec)
```

　有几个内置函数，如avg()、sum()、count()等，用于对表或特定表列执行所谓的聚合数据计算。

```
SQL> SELECT COUNT(*) AS "RECORDS" FROM CUSTOMERS; 
+---------+
| RECORDS |
+---------+
|       7 |
+---------+
1 row in set (0.00 sec)
```



## 日期表达式

------

　日期表达式返回当前系统日期和时间值：

```
SQL>  SELECT CURRENT_TIMESTAMP;
+---------------------+
| Current_Timestamp   |
+---------------------+
| 2009-11-12 06:40:23 |
+---------------------+
1 row in set (0.00 sec)
```

　另一个日期表达式如下所示：

```
SQL>  SELECT  GETDATE();;
+-------------------------+
| GETDATE                 |
+-------------------------+
| 2009-10-22 12:07:18.140 |
+-------------------------+
1 row in set (0.00 sec)
```


