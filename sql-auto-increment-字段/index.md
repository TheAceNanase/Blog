# SQL-AUTO INCREMENT 字段



## SQL AUTO INCREMENT 字段

------

　Auto-increment 会在新记录插入表中时生成一个唯一的数字。



## AUTO INCREMENT 字段

------

　我们通常希望在每次插入新记录时自动创建主键字段的值。            
        

　我们可以在表中创建一个自动增量（auto-increment）字段。



## 用于 MySQL 的语法

------

　以下SQL语句将 "Persons" 表中的“ID”列定义为自动递增（auto-increment）主键字段：

```
CREATE TABLE Persons                
(                
ID int NOT NULL AUTO_INCREMENT,                
LastName varchar(255) NOT NULL,                
FirstName varchar(255),                
Address varchar(255),                
City varchar(255),                
PRIMARY KEY (ID)                
)
```

　MySQL使用AUTO_INREMENT关键字来执行自动增量（ auto-increment ）任务。 

　默认情况下，AUTO_INREMENT的起始值为1，每个新记录增加1。

　若要以其他值开始AUTO_INREMENT序列，请使用以下SQL语法：

```
ALTER TABLE Persons AUTO_INCREMENT=100       
```

　要在 "Persons" 表中插入新记录，我们不需要为"ID"栏指定值（自动添加唯一值）：

```
INSERT INTO Persons (FirstName,LastName) VALUES ('Lars','Monsen')        
```

　上面的SQL语句在 "Persons" 表中插入一个新记录。“ID”栏将得到唯一值。"FirstName"栏设置为"Lars"，"LastName"栏设置为"Monsen"。



## 用于 SQL Server 的语法

------

　以下SQL语句将 "Persons" 表中的“ID”列定义为自动递增（ auto-increment ）主键字段：

```
CREATE TABLE Persons                
(                
ID int IDENTITY(1,1) PRIMARY KEY,                
LastName varchar(255) NOT NULL,                
FirstName varchar(255),                
Address varchar(255),                
City varchar(255)                
)       
```

　MS SQL Server使用IDENTITY关键字执行自动增量（ auto-increment ）任务。

　在上面的示例中，IDENTITY的起始值为1，每个新记录增量为1。

　提示：指定“ID”列以10开头，并递增5，将标识（ identity ）更改为IDENTITY（10,5）。

　要在 "Persons" 表中插入新记录，我们不需要为"ID"栏指定值（自动添加唯一值）：

```
INSERT INTO Persons (FirstName,LastName) VALUES ('Lars','Monsen')        
```

　上面的 SQL 语句在 "Persons" 表中插入一个新记录。“ID”栏将得到唯一值。"FirstName"栏设置为"Lars"，"LastName"栏设置为"Monsen"。



## 用于 Access 的语法

------

　以下 SQL 语句将 "Persons" 表中的“ID”列定义为自动递增（ auto-increment ）主键字段：            
        

```
CREATE TABLE Persons                
(                
ID Integer PRIMARY KEY AUTOINCREMENT,                
LastName varchar(255) NOT NULL,                
FirstName varchar(255),                
Address varchar(255),                
City varchar(255)                
)       
```

　MS Access使用 AUTOINCREMENT 关键字执行自动增量（ auto-increment ）任务。            
        

　默认情况下，AUTOINCREMEN的起始值为1，每个新记录递增 1。

　**提示：**指定“ID”栏以10开头，并递增5，将自动递增（ autoincrement ）更改为自动递增（105）（ AUTOINCREMENT(10,5)）。

　要在 "Persons" 表中插入新记录，我们不需要为"ID"栏指定值（自动添加唯一值）：

```
INSERT INTO Persons (FirstName,LastName) VALUES ('Lars','Monsen')        
```

　上面的 SQL 语句在 "Persons" 表中插入一个新记录。“ID”栏将得到唯一值。"FirstName"栏设置为"Lars"，"LastName"栏设置为"Monsen"。



## 语法 for Oracle

------

　在 Oracle 中，代码有点复杂。

　您必须使用序列（ sequence ）对象（该对象生成数字序列）创建自动增量（ auto-increment ）字段。

　使用以下CREATSEQUENT语法：

```
CREATE SEQUENCE seq_person                
MINVALUE 1                
START WITH 1                
INCREMENT BY 1                
CACHE 10        
```

　上面的代码创建了一个名为seq_pean的序列( sequence) 对象，它以1开头，以1递增。此对象缓存10个值以提高性能。缓存选项指定要存储多少序列值以提高访问速度。

　要在"Persons" 表中插入新记录，我们必须使用nextval函数，该函数从seq_hor序列检索下一个值：

```
INSERT INTO Persons (ID,FirstName,LastName) VALUES (seq_person.nextval,'Lars','Monsen')       
```

　上面的SQL语句在 "Persons" 表中插入一个新记录。"ID" 列从 seq_person 序列中分配下一个数字。"FirstName"栏设置为"Lars"，"LastName"栏设置为"Monsen"。            
        

