# SQL-约束

## SQL 约束

------

　约束是作用于数据表中列上的规则，用于限制表中数据的类型。约束的存在保证了数据库中数据的精确性和可靠性。

　约束有列级和表级之分，列级约束作用于单一的列，而表级约束作用于整张数据表。

　下面是 SQL 中常用的约束，这些约束虽然已经在关系型数据库管理系统一章中讨论过了，但是仍然值得在这里回顾一遍。

- [NOT NULL 约束](https://www.w3cschool.cn/sql/6tlpzfpb.html)：保证列中数据不能有 NULL 值
- [DEFAULT 约束](https://www.w3cschool.cn/sql/jm8e9fpj.html)：提供该列数据未指定时所采用的默认值
- [UNIQUE 约束](https://www.w3cschool.cn/sql/wxzqsfpc.html)：保证列中的所有数据各不相同
- [主键约束](https://www.w3cschool.cn/sql/vle8zfpd.html)：唯一标识数据表中的行/记录
- [外键约束](https://www.w3cschool.cn/sql/5dycsfpf.html)：唯一标识其他表中的一条行/记录
- [CHECK 约束](https://www.w3cschool.cn/sql/fsq7hfph.html)：此约束保证列中的所有值满足某一条件
- [索引](https://www.w3cschool.cn/sql/cuj91oz2.html)：用于在数据库中快速创建或检索数据

　约束可以在创建表时规定（通过 CREATE TABLE 语句），或者在表创建之后规定（通过 ALTER TABLE 语句）。



## SQL创建约束

------

　当使用CREATE TABLE语句创建表时，或者在使用ALTER TABLE语句创建表之后，可以指定约束。

　语法

```
CREATE TABLE table_name (
    column1 datatype constraint,
    column2 datatype constraint,
    column3 datatype constraint,
    ....
);
```

### SQL CREATE TABLE + CONSTRAINT 语法

```
CREATE TABLE table_name                
(                
column_name1 data_type(size) constraint_name,                
column_name2 data_type(size) constraint_name,                
column_name3 data_type(size) constraint_name,                
....                
);      
```



## 删除约束

------

　任何现有约束都可以通过在 ALTER TABLE 命令中指定 DROP CONSTRAINT 选项的方法删除掉。

　例如，要去除 EMPLOYEES 表中的主键约束，可以使用下述命令：

```
ALTER TABLE EMPLOYEES DROP CONSTRAINT EMPLOYEES_PK;
```

　一些数据库实现可能提供了删除特定约束的快捷方法。例如，要在 Oracle 中删除一张表的主键约束，可以使用如下命令：

```
ALTER TABLE EMPLOYEES DROP PRIMARY KEY;
```

　某些数据库实现允许禁用约束。这样与其从数据库中永久删除约束，你可以只是临时禁用掉它，过一段时间后再重新启用。



## 完整性约束

------

　完整性约束用于保证关系型数据库中数据的精确性和一致性。对于关系型数据库来说，数据完整性由参照完整性（referential integrity，RI）来保证。

　有很多种约束可以起到参照完整性的作用，这些约束包括主键约束（Primary Key）、外键约束（Foreign Key）、唯一性约束（Unique Constraint）以及上面提到的其他约束。

