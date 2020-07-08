# SQL-NULL 值 – IS NULL 和 IS NOT NULL


# SQL NULL 值 – IS NULL 和 IS NOT NULL

------

　NULL 空值代表丢失的未知数据。

　默认情况下，表列可以保存 NULL 值。

　本章解释 IS NULL 和 IS NOT NULL 操作符。

​            
​        

## SQL NULL 值

------

　如果表中的列是可选的，那么我们可以插入一个新记录或更新一个现有记录，而无需向列添加一个值。这意味着该字段将存储为 NULL 。            
        

　NULL 值的处理与其他值不同。

　NULL 为未知或不适当值的占位符。

​            　![Note](https://atts.w3cschool.cn/attachments/image/lamp.gif)**注释：**无法比较 NULL 和 0；它们是不等价的。



## SQL 的 NULL 值处理

------

　请看下面的 "Persons" 表：

| P_Id | LastName  | FirstName | Address   | City      |
| ---- | --------- | --------- | --------- | --------- |
| 1    | Hansen    | Ola       |           | Sandnes   |
| 2    | Svendson  | Tove      | Borgvn 23 | Sandnes   |
| 3    | Pettersen | Kari      |           | Stavanger |

　如果 "Persons" 表 "Address" 一栏是可选的。这意味着，如果在 "Address" 列中插入一个没有值的记录，则 "Address" 列将用 NULL 值保存。

　那么如何测试null的值呢？

　您不能使用比较操作符测试 NULL 值，例如=、<或<>。

　我们必须使用 IS NULL 和 IS NOT NULL 操作符。



## SQL IS NULL

------

　我们如何才能选择 "Address" 列中有 NULL 值的记录？

　我们必须使用 IS NULL 操作符：

```
SELECT LastName,FirstName,Address FROM Persons WHERE Address IS NULL
```

　结果集如下所示：

| LastName  | FirstName | Address |
| --------- | --------- | ------- |
| Hansen    | Ola       |         |
| Pettersen | Kari      |         |

​            　![Note](https://atts.w3cschool.cn/attachments/image/lamp.gif)**提示：**总是使用 IS NULL 来查找 NULL 值。



## SQL IS NOT NULL

------

　我们如何才能选择 "Address" 列中没有 NULL 值的记录？

　我们必须使用 IS NOT NULL 操作符：

```
SELECT LastName,FirstName,Address FROM Persons WHERE Address IS NOT NULL
```

　结果集如下所示：

| LastName | FirstName | Address   |
| -------- | --------- | --------- |
| Svendson | Tove      | Borgvn 23 |
