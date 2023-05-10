## GIN、 Begoo、 IRIS 介绍

### Gin:

```
Gin 是一个用 Go (Golang) 编写的 web 框架。它是一个类似于 martini 但拥有更好性能的 API 框架, 由于 httprouter，速度提高了近 40 倍。如果你是性能和高效的追求者, 你会爱上 Gin.

快速：基于 Radix 树的路由，小内存占用。没有反射。可预测的 API 性能。

支持中间件：传入的 HTTP 请求可以由一系列中间件和最终操作来处理。例如：Logger，Authorization，GZIP，最终操作 DB。

Crash 处理：Gin 可以 catch 一个发生在 HTTP 请求中的 panic 并 recover 它。这样，你的服务器将始终可用。例如，你可以向 Sentry 报告这个 panic！

JSON 验证：Gin 可以解析并验证请求的 JSON，例如检查所需值的存在。

路由组：更好地组织路由。是否需要授权，不同的 API 版本…… 此外，这些组可以无限制地嵌套而不会降低性能。

错误管理：Gin 提供了一种方便的方法来收集 HTTP 请求期间发生的所有错误。最终，中间件可以将它们写入日志文件，数据库并通过网络发送。

内置渲染：Gin 为 JSON，XML 和 HTML 渲染提供了易于使用的 API。

可扩展性：新建一个中间件非常简单，去查看示例代码吧。
```

### Beego：

```
bee 工具是一个为了协助快速开发 beego 项目而创建的项目，通过 bee 您可以很容易的进行 beego 项目的创建、热编译、开发、测试、和部署。

简单化：RESTful 支持、MVC 模型，可以使用 bee 工具快速地开发应用，包括监控代码修改进行热编译、自动化测试代码以及自动化打包部署。

智能化：支持智能路由、智能监控，可以监控 QPS、内存消耗、CPU 使用，以及 goroutine 的运行状况，让您的线上应用尽在掌握。

模块化：beego 内置了强大的模块，包括 Session、缓存操作、日志记录、配置解析、性能监控、上下文操作、ORM 模块、请求模拟等强大的模块，足以支撑你任何的应用。

高性能：beego 采用了 Go 原生的 http 包来处理请求，goroutine 的并发效率足以应付大流量的 Web 应用和 API 应用，目前已经应用于大量高并发的产品中。
```

### Iris：

```
专注于高性能
简单流畅的API
高扩展性
强大的路由和中间件生态系统
使用iris独特的表达主义路径解释器构建RESTful API
动态路径参数化或通配符路由与静态路由不冲突
使用重定向选项从URL中删除尾部斜杠
使用虚拟主机和子域名变得容易
分组API和静态或甚至动态子域名
net / http和negroni-like处理程序通过iris.FromStd兼容
针对任意Http请求错误 定义处理函数
支持事务和回滚
支持响应缓存
使用简单的函数嵌入资源并与go-bindata 保持兼容
mvc
上下文
高度可扩展的试图渲染(目前支持markdown,json,xml，jsonp等等)
正文绑定器和发送HTTP响应的便捷功能
限制请求正文
提供静态资源或嵌入式资产
本地化i18N
压缩（Gzip是内置的）
身份验证
Basic Authentication
OAuth, OAuth2 (支持27个以上的热门网站)
JWT 服务器
通过TLS提供服务时，自动安装和提供来自https://letsencrypt.org的证书
默认为关闭状态
在关闭，错误或中断事件时注册
连接多个服务器，完全兼容 net/http#Server
视图系统.支持五种模板引擎 完全兼容 html/template
Websocket库，其API类似于http://socket.io [如果你愿意，你仍然可以使用你最喜欢的]
热重启
Typescript集成 + Web IDE
Iris是最具特色的网络框架之一
```
