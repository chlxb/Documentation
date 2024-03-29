# SQLite 数据库

SQLite 是一个开源的嵌入式关系数据库，实现自包容、零配置、支持事务的SQL数据库引擎。其特点是高度便携、使用方便、结构紧凑、高效、可靠。 与其他数据库管理系统不同，SQLite 的安装和运行非常简单，在大多数情况下,只要确保SQLite的二进制文件存在即可开始创建、连接和使用数据库。如果您正在寻找一个嵌入式数据库项目或解决方案，SQLite是绝对值得考虑。

## 驱动

Go支持sqlite的驱动也比较多，但是好多都是不支持database/sql接口的

- https://github.com/mattn/go-sqlite3 支持database/sql接口，基于cgo(关于cgo的知识请参看官方文档或者本书后面的章节)写的
- https://github.com/feyeleanor/gosqlite3 不支持database/sql接口，基于cgo写的
- https://github.com/phf/go-sqlite3 不支持database/sql接口，基于cgo写的

目前支持database/sql的SQLite数据库驱动只有第一个，我目前也是采用它来开发项目的。采用标准接口有利于以后出现更好的驱动的时候做迁移。

安装：

```
go get github.com/mattn/go-sqlite3 // 不要添加 -u 选项
```

## 参考

[sqlite 官网](https://www.sqlite.org/docs.html)
[go-sqlite3 github](https://github.com/mattn/go-sqlite3)
[sqlite 菜鸟教程](https://www.runoob.com/sqlite/sqlite-create-database.html)