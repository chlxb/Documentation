# beego 框架指南

# 1. RESTfulAPI 项目

## 1.1 RESTful 介绍

> 1. 设计接口API RESTful API的特点
> * 基于“资源”，数据也好、服务也好，在RESTFul设计里一切都是资源。
> * 无状态。一次调用一般就会返回结果，不存在类似于“打开连接-访问数据-关闭连接”这种依赖于上一次调用的情况。
> * URL中通常不出现动词，只有名词
> * URL语义清晰、明确
> * 使用HTTP的GET、POST、DELETE、PUT来表示对于资源的增删改查
> * 使用JSON不使用XML

> 2. RESTful风格的接口设计尽量遵循以下原则 (参考RESTful API接口设计)
> * 使用HTTP动词表示增删改查资源， GET：查询，POST：新增，PUT：更新，DELETE：删除
> * 返回结果必须使用JSON
> * HTTP状态码，在REST中都有特定的意义：200，201,202,204,400,401,403,500。比如401表示用户身
> * 认证失败，403表示你验证身份通过了，但这个资源你不能操作
> * 如果出现错误，返回一个错误码
> * API必须有版本的概念，v1，v2，v3
> * 使用Token令牌来做用户身份的校验与权限分级，而不是Cookie
> * url中大小写不敏感，不要出现大写字母
> * 使用 - 而不是使用 _ 做URL路径中字符串连接
> * 有一份漂亮的文档

## 1.2 开发流程

1. 创建工程 
   
    指定目录下执行命令，生成名为 "test.com" 项目文件夹
   
   > bee api xxxx.com  // bee api test.com  
   >  运行项目， 在浏览器中 http://localhost:8080/swagger/ 可以看到项目接口（此处可能有问题，接口显示不对）
   > bee run -gendoc=true -downdoc=true   // 该命令运行后，支持文件改动时热更新

2. 设计beego数据结构模型
   
   > beego的MVC模型从工程结构上分为controllers、models、views；routers目录提供了接口的路由信息。
   
   * controllers：接口服务的具体实现，通常为业务的具体实现；
   * models：数据模型，往往和数据库的设计直接相关；
   * views: api项目没有views目录，视图模型
   * beego的数据结构设计时，通过传入初始值从而实现ORM框架的表结构创建及检查

3. 在 models目录中创建对应业务的数据模型，例如创建一个 banner 结构体，实现该模型的业务方法等、数据库设计等

4. controllers 中创建与 models中相对应的业务接口实现，beego支持通过注解自动生成基于swagger的API。在 controller中通过对相应接口添加注解，可以实现自动生成API的能力，API会自动添加到 router/commentsRouter_controllers.go 文件中。
   
   **其中注解至少包含 @router 才自动生成对应的API**，如下：
   
   ```
   // @Title PostAPI   API接口的标题
   // @router / [post]  这个是自动生成的api接口支持的请求方式
   func (o *BannerController) PostAPI() {
       // todo...
       o.Data["json"] = "this is response data, string or object"
       o.ServeJSON()
   }
   ```

5. router.go 中添加对应的页面路由（将 controllers中的业务注册进路由中）
   
   ```
   // get "http://localhost:8080/v1/banner"
   ns := beego.NewNamespace("/v1",
       beego.NSNamespace("banner",
           beego.NSInclude(
               &controllers.BannerController{},
           ),
       ),
   )
   beego.AddNamespace(ns)
   ```

注解详解

> @Title 接口名，空格之后的内容全部解析为 title
> 
> @Description 接口描述，空格之后的内容全部解析为 Description
> 
> @Param 传入参数，共五列，使用空格或者 tab 分割
> 
> 1. 参数名
> 
> 2. 参数类型，可以有的值是 formData、query、path、body、header。
>    
>         formData 表示是 post 请求的数据；
>         query 表示带在 url 之后的参数；
>         path 表示请求路径上得参数，比如/user/:id中的id；
>         body 表示是一个 raw 数据请求；
>         header 表示带在 header 信息中得参数，比如"Content-Type":"application/json"。
> 
> 3. 数据类型, int,object(swagger会表示为json对象)
> 
> 4. 该参数是否必须，true,false
> 
> 5. 参数注释
> 
> @Success 成功返回给客户端的信息，共三个参数，之间用空格分开
> 
> 1. status code
> 2. 返回的类型，必须使用 {} 包含，比如 {int},{object}
> 3. 返回的对象或者字符串信息，例如{object} 类型， models.ZDTProduct.ProductList 就表示 /models/ZDTProduct 目录下的 ProductList 对象
>    @Failure 失败返回的信息，包含两个参数，使用空格分隔
> 4. status code
> 5. 错误信息
>    @router 两个参数，使用空格分隔
> 6. 请求的路由地址，支持正则和自定义路由
> 7. 请求方法,放在 []之中，如果有多个方法，那么使用 , 分隔

示例如下：

```
// @Title  Delete 用户接口
// @Description 用来删除指定的用户，delete the object
// @Param   userId    path string     true  "The objectId you want to delete" 
// @Success 200 {string}  delete success!
// @Failure 403   userId is empty
// @router /:userId   [delete]
```

# Beego

beego orm一个Go进行ORM操作的十分轻量的 ORM 框架，采用了Go style方式对数据库进行操作，实现了struct到数据表记录的映射。beego orm是目前开源的Go ORM框架中实现比较完整的一个库，而且运行效率相当不错，功能也基本能满足需求。

beego orm是支持database/sql标准接口的ORM库，所以理论上来说，只要数据库驱动支持database/sql接口就可以无缝的接入beego orm。作者测试支持的驱动包如下：

- Mysql: github/go-mysql-driver/mysql
- PostgreSQL: github.com/lib/pq
- SQLite: github.com/mattn/go-sqlite3
- Mysql: github.com/ziutek/mymysql/godrv

暂未支持的驱动包：

- MsSql: github.com/denisenkom/go-mssqldb
- MS ADODB: github.com/mattn/go-adodb
- Oracle: github.com/mattn/go-oci8
- ODBC: bitbucket.org/miquella/mgodbc

## 安装

```
go get github.com/astaxie/beego
```

## 初始化

# 参考

* [Beego github](https://github.com/beego/beego) 

* [beego 官方example - github](https://github.com/beego/beego-example)

* [Beego 中文文档](https://beego.gocn.vip)

* [基于beego 开发Restful项目](https://blog.csdn.net/rabbit0206/article/details/104200044)

* [begoo 案例 - github](https://github.com/aceld/iHome)

* 