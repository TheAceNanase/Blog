# MySQL安装

## MySQL 安装

所有平台的 MySQL 下载地址为： [MySQL 下载](https://www.mysql.com/cn/downloads/). 挑选你需要的 MySQL Community Server 版本及对应的平台。

> **注意：**安装过程我们需要通过开启管理员权限来安装，否则会由于权限不足导致无法安装。

------

## Linux / UNIX 上安装 MySQL

Linux 平台上推荐使用 RPM 包来安装 MySQL ，MySQL AB 提供了以下 RPM 包的下载地址：

- **MySQL** - MySQL 服务器。如果不是只想连接运行在另一台机器上的 MySQL 服务器，请你选择该选项。
- **MySQL-client** - MySQL 客户端程序，用于连接并操作 Mysql 服务器。
- **MySQL-devel** - 库和包含文件，如果你想要编译其它如 Perl 模块等 MySQL 客户端，则需要安装该 RPM 包。
- **MySQL-shared** - 该软件包包含某些语言和应用程序需要动态装载的共享库(libmysqlclient.so*)，使用 MySQL。
- **MySQL-bench** - MySQL 数据库服务器的基准和性能测试工具。

以下安装 MySQL RMP 的实例是在 SuSE Linux 系统上进行，当然该安装步骤也适合应用于其他支持 RPM 的 Linux 系统，如: CentOS。

​    

安装前，我们可以检测系统是否自带安装 MySQL：

```
rpm -qa | grep mysql
```

如果你系统有安装，那可以选择进行卸载:

```
rpm -e mysql　　// 普通删除模式
rpm -e --nodeps mysql　　// 强力删除模式，如果使用上面命令删除时，提示有依赖的其它文件，则用该命令可以对其进行强力删除
```

### MySQL 安装步骤如下：

使用 root 用户登陆你的 Linux 系统。

下载 MySQL RPM 包，下载地址为：[MySQL 下载](http://www.mysql.com/downloads)。

通过以下命令执行 MySQ L安装，rpm 包为你下载的 rpm 包：

```
[root@host]# rpm -i MySQL-5.0.9-0.i386.rpm
```

以上安装MySQL服务器的过程会创建MySQL用户，并创建一个MySQL配置文件my.cnf。

你可以在/usr/bin和/usr/sbin中找到所有与MySQL相关的二进制文件。所有数据表和数据库将在/var/lib/mysql目录中创建。

以下是一些MySQL可选包的安装过程，你可以根据自己的需要来安装：

```
[root@host]# rpm -i MySQL-client-5.0.9-0.i386.rpm
[root@host]# rpm -i MySQL-devel-5.0.9-0.i386.rpm
[root@host]# rpm -i MySQL-shared-5.0.9-0.i386.rpm
[root@host]# rpm -i MySQL-bench-5.0.9-0.i386.rpm
```

------

## Window上安装MySQL

Window上安装MySQL相对来说会较为简单，你只需要在 [MySQL 下载](http://www.mysql.com/downloads)中下载window版本的MySQL安装包，并解压安装包。

双击 setup.exe 文件，接下来你只需要安装默认的配置点击"next"即可，默认情况下安装信息会在C:\mysql目录中。

接下来你可以通过"开始" =》在搜索框中输入 " cmd" 命令 =》 在命令提示符上切换到 C:\mysql\bin 目录，并输入一下命令：

```
mysqld.exe --console
```

如果安装成功以上命令将输出一些MySQL启动及InnoDB信息。

------

## 验证MySQL安装

在成功安装MySQL后，一些基础表会表初始化，在服务器启动后，你可以通过简单的测试来验证MySQL是否工作正常。

使用 mysqladmin 工具来获取服务器状态：

使用 mysqladmin 命令俩检查服务器的版本,在linux上该二进制文件位于 /usr/bin on linux ，在window上该二进制文件位于C:\mysql\bin 。

```
[root@host]# mysqladmin --version
```

linux上该命令将输出以下结果，该结果基于你的系统信息：

```
mysqladmin  Ver 8.23 Distrib 5.0.9-0, for redhat-linux-gnu on i386
```

如果以上命令执行后未输入任何信息，说明你的MySQL未安装成功。

------

## 使用 MySQL Client(MySQL客户端) 执行简单的SQL命令

你可以在 MySQL Client(MySQL客户端) 使用 MySQL 命令连接到MySQL服务器上，默认情况下MySQL服务器的密码为空，所以本实例不需要输入密码。

命令如下：

```
[root@host]# mysql
```

以上命令执行后会输出 mysql > 提示符，这说明你已经成功连接到 MySQL 服务器上，你可以在 mysql > 提示符执行 SQL 命令：

```
mysql> SHOW DATABASES;
+----------+
| Database |
+----------+
| mysql    |
| test     |
+----------+
2 rows in set (0.13 sec)
```

------

## MySQL安装后需要做的

MySQL 安装成功后，默认的 root 用户密码为空，你可以使用以下命令来创建root用户的密码：

```
[root@host]# mysqladmin -u root password "new_password";
```

现在你可以通过以下命令来连接到MySQL服务器：

```
[root@host]# mysql -u root -p
Enter password:*******
```

**注意：**在输入密码时，密码是不会显示了，你正确输入即可。

------

## Linux系统启动时启动 MySQL

如果你需要在Linux系统启动时启动 MySQL 服务器，你需要在 /etc/rc.local 文件中添加以下命令：

```
/etc/init.d/mysqld start
```

同样，你需要将 mysqld 二进制文件添加到 /etc/init.d/ 目录中。
