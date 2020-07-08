# SQL-WHERE Clause(查询子句)

## SQL WHERE 子句

WHERE 子句用于过滤记录。

WHERE 子句用于提取满足指定标准的记录。 


### SQL WHERE 语法

```
SELECT column1, column2, ...
FROM table_name
WHERE condition;
```

> 注意： WHERE子句不仅用于SELECT语法，还用于UPDATE，DELETE语法等！


　WHERE子句可以与以下类型的SQL语句一起使用：


* UPDATE
* DELETE

UPDATE语句：

<pre lang="sql"><a><i></i></a><code class="sql hljs"><span class="hljs-keyword">UPDATE</span> <span class="hljs-string">&#34;table_name&#34;</span>
<span class="hljs-keyword">SET</span> <span class="hljs-string">&#34;column_1&#34;</span> = [<span class="hljs-keyword">new</span> <span class="hljs-keyword">value</span>]
<span class="hljs-keyword">WHERE</span> <span class="hljs-string">&#34;condition&#34;</span>;</code></pre>

DELETE语句：

<pre lang="sql"><a><i></i></a><code class="sql hljs"><span class="hljs-keyword">DELETE</span> <span class="hljs-keyword">FROM</span> <span class="hljs-string">&#34;table_name&#34;</span>
<span class="hljs-keyword">WHERE</span> <span class="hljs-string">&#34;condition&#34;</span>;</code></pre>

## 演示数据库

以下是 "Customers" 表中的数据：

| CustomerID | CustomerName | ContactName | Address | City | PostalCode | Country |
| - | - | - | - | - | - | - |
| 1| Alfreds Futterkiste | Maria Anders | Obere Str. 57 | Berlin | 12209 | Germany |
| 2 | Ana Trujillo Emparedados y helados | Ana Trujillo | Avda. de la Constitución 2222 | México D.F. | 05021 | Mexico |
| 3 | Antonio Moreno Taquería | Antonio Moreno | Mataderos 2312 | México D.F. | 05023 | Mexico |
| 4 | Around the Horn | Thomas Hardy | 120 Hanover Sq. | London | WA1 1DP | UK |
| 5 | Berglunds snabbköp | Christina Berglund | Berguvsvägen 8 | Luleå | S-958 22 | Sweden |

## WHERE 子句实例

以下SQL语句从"Customers"表中选择其国家为"Mexico"的所有客户：

## 实例
```
SELECT * FROM Customers WHERE Country='Mexico';
```
也可以使用OR运算符的查询子句：

## 实例
```
SELECT * FROM Customers WHERE Country='Mexico' OR PostalCode='05021';
```




    

    
## 文本字段与数值字段

SQL在文本值周围使用单引号（大多数数据库系统也接受双引号）。
如果是数值字段，则不要使用引号。

 ## 实例
```
SELECT * FROM Customers WHERE CustomerID=1;
```

## WHERE 子句中的运算符

WHERE子句中可以使用以下运算符：
        
| 运算符 | 描述 |
| - | - |
| = | 等于 |
| <> | 不等于。 **注意**：在某些版本的SQL中，这个操作符可能写成！=|
| > | 大于 |
| < | 小于 |
| >= | 大于等于 |
| <= | 小于等于 |
| BETWEEN | 在某个范围内 |
| LIKE | 搜索某种模式 |
| IN | 为列指定多个可能的值 |


