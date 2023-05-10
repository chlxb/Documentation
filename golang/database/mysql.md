# MySQL数据库

目前Internet上流行的网站构架方式是LAMP，其中的M即MySQL, 作为数据库，MySQL以免费、开源、使用方便为优势成为了很多Web开发的后端数据库存储引擎。

## MySQL驱动

Go中支持MySQL的驱动目前比较多，有如下几种，有些是支持database/sql标准，而有些是采用了自己的实现接口,常用的有如下几种:

- https://github.com/go-sql-driver/mysql 支持database/sql，全部采用go写。
- https://github.com/ziutek/mymysql 支持database/sql，也支持自定义的接口，全部采用go写。
- https://github.com/Philio/GoMySQL 不支持database/sql，自定义接口，全部采用go写。

接下来的例子我主要以第一个驱动为例(我目前项目中也是采用它来驱动)，也推荐大家采用它，主要理由：

- 这个驱动比较新，维护的比较好
- 完全支持 database/sql 接口
- 支持 keepalive，保持长连接,虽然 mymysql 也支持 keepalive，但不是线程安全的，这个从底层就支持了 keepalive。

驱动安装：

```
go get -u github.com/go-sql-driver/mysql
```

## 安装MySQL

在MySQL官网。按照自己的电脑系统及芯片选择对应的下载链接，下载软件包，然后点击安装，安装成功后，在终端测试:
MySQL 默认服务端口是 **3306/TCP**, MySQL 还有其他端口提供给不同功能使用

查看版本： `mysql --version`

登录root账户:

```
mysql -uroot -p123456 
输入密码
```

命令格式：mysql -u[username] -p 之后输入密码

> -u: mysql 用户名
> 
> -p: mysql 密码
> 
> mysql -h 127.0.0.1 -u root -p  //登录远程数据库 或 docker数据库

终端常用命令：

1. 创建数据库:
   
   ```
   // 创建名为 test 的数据库
   create database test; // create database dbname;
   // 删除数据库
   drop database test;  // drop database dbname;
   // 打开数据库
   use test; //打开 test 数据库
   // 查看所有的数据表
   show tables;
   // 显示表结构
   desc userinfo; // 显示 userinfo 表的结构
   ```

// 创建表
create table userinfo(
    uid int(10) auto_increment,
    username varchar(64) null default null,
    department varchar(64) null default null,
    created date null default null,
    primary key (uid)
);

create table `userinfo`(
    `uid` int(10) auto_increment,
    `username` varchar(64) null default null,
    `department` varchar(64) null default null,
    `created` date null default null,
    primary key (uid)
);

create table `userinfo`(
    `uid` int(10) primary key auto_increment,
    `username` varchar(64) null default null,
    `department` varchar(64) null default null,
    `created` date null default null,
);

```
## golang
```

/* 打开一个注册过的数据库驱动
    @driverName 驱动名称 "mysql"
    @dataSourceName 数据库连接的配置信息，支持如下内容：
        user@unix(/path/to/socket)/dbname?charset=utf8
        user:password@tcp(localhost:5555)/dbname?charset=utf8
        user:password@/dbname
        user:password@tcp([de:ad:be:ef::ca:fe]:80)/dbname
*/
sql.Open()
// 连接数据库  
db, err := sql.Open("mysql", "root:Lxb!123456@tcp(127.0.0.1:3306)/") // username:password@tcp(id:port)/dbname?charset=utf8") /后面的可省略

```
## 参考：
------
- [MySQL 官网](https://dev.mysql.com/downloads/mysql/)
- [MySQL-github](https://github.com/go-sql-driver/mysql)
- [MySQL-wiki](https://github.com/go-sql-driver/mysql/wiki/Examples)
- [MySQL 安装教程 - csdn](https://blog.csdn.net/WHEeeee/article/details/115286833)
- [Mac安装MySQL详细教程](https://blog.csdn.net/guorenhao/article/details/124508441)
- [Go语言操作MySQL语言基础知识 - Golang发烧友](https://zhuanlan.zhihu.com/p/347206310)
- [Golang MySQL安装及配置（Windows）- ElvisChiu](https://zhuanlan.zhihu.com/p/533377930)
- [GoLang如何操作mysql - csdn](https://blog.csdn.net/m0_53157173/article/details/126471955)
- [菜鸟教程 MySQL](https://www.runoob.com/mysql/mysql-select-database.html)
```
