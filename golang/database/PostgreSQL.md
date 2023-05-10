# 使用PostgreSQL数据库

PostgreSQL 是一个自由的对象-关系数据库服务器(数据库管理系统)，它在灵活的 BSD-风格许可证下发行。它提供了相对其他开放源代码数据库系统(比如 MySQL 和 Firebird)，和对专有系统比如 Oracle、Sybase、IBM 的 DB2 和 Microsoft SQL Server的一种选择。

PostgreSQL和MySQL比较，它更加庞大一点，因为它是用来替代Oracle而设计的。所以在企业应用中采用PostgreSQL是一个明智的选择。

## 驱动

Go实现的支持PostgreSQL的驱动也很多，因为国外很多人在开发中使用了这个数据库。

- https://github.com/lib/pq 支持database/sql驱动，纯Go写的
- https://github.com/jbarham/gopgsqldriver 支持database/sql驱动，纯Go写的
- https://github.com/lxn/go-pgsql 支持database/sql驱动，纯Go写的

在下面的示例中我采用了第一个驱动，因为它目前使用的人最多，在GitHub上也比较活跃。

安装

```
go get github.com/lib/pq
```

## PostgreSQL 安装

多种安装方式，具体参考官网

- 命令行安装
  
  - rpm // linux
  - yum // linux
  - homebrew // macos

- 下载安装器进行安装,多平台支持:
  
   https://www.enterprisedb.com/downloads/postgres-postgresql-downloads

- Postgres.app macos

### 启动

1. 命令行：

执行安装路径下面的启动脚本 或者通过点击启动界面 SQL Shell （其实就是同一个脚本）,会要求输入Server、database、port、username、密码等信息，可以直接回车使用默认的

```
/Library/PostgreSQL/15/scripts/runpsql.sh
```

2. pgAdmin 可视化软件

## 常用命令

创建数据库：

```
CREATE DATABASE dbname; // 创建
createdb [option...] [dbname [description]] //createdb 是 SQL 命令 CREATE DATABASE 的封装
```

## 配置文件

PostgreSQL的配置文件主要有如下3个(postgresql.conf,pg_hba.conf,pg_ident.conf),一般放在安装时设置的 data 目录里，可以通过postgresql.conf 配置文件指定

```
data_directory = '/mnt/data/postgresdb/data'        # use data in another directory
#hba_file = 'ConfigDir/pg_hba.conf'    # host-based authentication file
#ident_file = 'ConfigDir/pg_ident.conf'    # ident configuration file
```

可以以超级用户登录数据库，用以下命令查找:

```
// 查找配置文件
postgres=# select name, setting from pg_settings where category='File Locations'; 
// find 命令查找（需要root用户权限）
find / -name pg_hba.conf
find /Library/PostgreSQL/15/data -name pg_hba.conf
```

1、postgresql 系统可通过手动配置的文件分为3部分

- 主服务器配置文件：postgresql.conf
- 基于主机认证配置文件：pg_hba.conf
- 用于用户名称映射的配置文件：pg_ident.conf

## 参考

[PostgresSQL 官方文档](https://www.postgresql.org/download/linux/redhat/)
[pq github](https://github.com/lib/pq)
[PostgreSQL 菜鸟教程](https://www.runoob.com/postgresql/postgresql-tutorial.html)
[PostgreSQL 配置文件详解](https://blog.csdn.net/weixin_42633805/article/details/120638847)
[PostgreSQL 使用(马克JAVA社区)](http://www.mark-to-win.com/tutorial/51623.html)