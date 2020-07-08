# SQL选择数据库 USE语句



## SQL 选择数据库 USE语句

------

　当SQL Schema中有多个数据库时，在开始操作之前，需要选择一个执行所有操作的数据库。

　SQL USE语句用于选择SQL架构中的任何现有数据库。

### 句法

　USE语句的基本语法如下所示 :

```
USE DatabaseName;
```

　数据库名称在RDBMS中必须是唯一的。



## 实例

------

　您可以查看可用的数据库，如下所示：

```
SQL> SHOW DATABASES;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| AMROOD             |
| TUTORIALSPOINT     |
| mysql              |
| orig               |
| test               |
+--------------------+
6 rows in set (0.00 sec)
```

　现在，如果您想使用AMROOD数据库，那么您可以执行以下SQL命令并开始使用AMROOD数据库。

```
SQL> USE AMROOD;
```


