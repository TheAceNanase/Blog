# SQL-SELECT DISTINCT语法


## SQL SELECT DISTINCT 语法

SELECT DISTINCT语法用于仅返回不同的（different）值。

在一张表内，一列通常包含许多重复的值; 有时你只想列出不同的（different）值。

SELECT DISTINCT语句用于仅返回不同的（different）值。

SQL SELECT DISTINCT语法如下所示：

```
SELECT DISTINCT column1, column2, ...
FROM table_name;
```

 ## 演示数据库

下面是罗斯文示例数据库中 "Customers" 表的数据：

| CustomerID | CustomerName | ContactName | Address | City | PostalCode | Country |
| - | - | - | - | - | - | - |
| 1 | Alfreds Futterkiste | Maria Anders | Obere Str. 57 | Berlin | 12209 | Germany |
| 2 | Ana Trujillo Emparedados y helados | Ana Trujillo | Avda. de la Constitución 2222 | México D.F. | 05021 | Mexico |
| 3 | Antonio Moreno Taquería | Antonio Moreno | Mataderos 2312 | México D.F. | 05023 | Mexico |
| 4 | Around the Horn | Thomas Hardy | 120 Hanover Sq. | London | WA1 1DP | UK |
| 5 | Berglunds snabbköp | Christina Berglund | Berguvsvägen 8 | Luleå | S-958 22 | Sweden |

 ## SELECT实例
以下SQL语句从“Customers”表中的“Country”列中选择所有（包括重复）值：

代码示例：

```
SELECT Country FROM Customers;
```
以上查询的结果：

<pre lang="sql"><a><i></i></a><code class="sql hljs"><b>Country</b>
Germany
Mexico
Mexico
UK
<p>Sweden</p></code></pre>

现在，让我们在上面的SELECT语法中使用DISTINCT关键字并查看结果。

## SELECT DISTINCT 实例

以下SQL语句仅从"Customers" 表中的 "Country" 列中选择DISTINCT值：

## 实例1
```
SELECT DISTINCT Country FROM Customers;
```
查询结果：

<pre lang="sql"><a><i></i></a><code class="sql hljs">Country
Germany
Mexico
UK
Sweden</code></pre>

以下SQL语句列出了不同（distinct）客户国家的数量： 

## 实例2
```
SELECT COUNT(DISTINCT Country) FROM Customers; 
```
由于在Microsoft Access数据库中不支持COUNT(DISTINCT *column_name*)。在我们的示例中Firefox和Microsoft Edge使用Microsoft Access。

