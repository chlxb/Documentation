# go 包管理
## go 有三中包管理方式
1. go path 和 GOROOT （1.5版本之前）
    
    GOROOT：go的源码存放目录我们可以在其src目录下面存放源码

    GOPATH:使用的时候项目必须要放在GOPATH目录下，但是不同工程需要不同的依赖包，如果有多个工程使用同一个目录，会使得包管理变得复杂。

    GOPATH目录下出了src目录还有bin和pkg目录其中bin是go install后的可执行文件，而pkg存在依赖包编译的中间文件，一般后缀.a结尾用于工程编译，pkg目录下面还有平台目录/linux_adm64，src目录存放自己写的项目，go mod开启则不会从goPath下面查找

3. go vendor (1.6版本以后)

    为了解决多个工程共享GOPATH目录，go开启了vendor目录，，每个工程的依赖包直接放到vendor目录下，这样不同的工程可以存放自己所需的各种依赖到vendor目录中。

3. go module (1.11 版本开始)

    了应对go path的缺点，从go1.11版本就推出了go modules包管理方式。开启了这种方式，寻找除了标准库包时，就会到$GOPATH/pkg/mod目录下查找，这是个go mod方式存放包的路径。

    vendor目录的依赖包需要手动加入，没有依赖的版本记录，对依赖包的升级困难，在1.13版本之后go mod默认开启，解决了必须将依赖包放在GOPATH目录下的问题，并且可以在go.mod中配置工程名，在go.mod中还配置了不同版本的依赖信息，支持自动下载指定依赖，可以自动扫描工程依赖包信息加入go.mod, 通过go get下载的依赖包也会自动加入go.mod,还兼容了vendor目录，可以使用go mod vendor命令自动将依赖包放在工程的子目录下。

## 存在vendor GOPATH和GOROOT
工程必须放在GOPATH/src目录下，如果 go vendor，GOPATH 和GOROOT目录存在依赖的情况，go build寻找引用依赖的顺序：
- 首先在vendor目录下查找依赖
- 如果找不到则会去找上一级目录直到到GOPATH/src下
- 不存在的话则去GOROOT目录下找
- 最后到GOPATH目录寻找

## Go Module
|   命令    | 说明 |
|:--------|:----------| 
| init | 初始化go mod，命令：go mod init xxx(package name)|
| download| download modules to local cache(全局下载依赖包)|
| edit | edit go.mod from tools or scripts（编辑go.mod)|
| graph	| print module requirement graph (打印模块依赖图) |
|verify	| initialize new module in current directory（在当前目录初始化mod）|
|tidy |	add missing and remove unused modules(拉取缺少的模块，移除不用的模块)|
|vendor	| make vendored copy of dependencies(将依赖复制到vendor下)|
|verify	| verify dependencies have expected content (验证依赖是否正确）|
|why |	explain why packages or modules are needed(解释为什么需要依赖)|


常见命令：
```
go env #查看环境配置
go env -w GOPROXY=https://goproxy.io,direct // 配置代理
go env -w GO111MODULE=on # 开启 go mod 模式

go mod init # 在 GOPATH 目录中：使用 go mod init 可以直接生成 以项目名作为名称的 module
go mod init name # 在GOPATH目录之外：初始化的时候必须指定 module 名
go mod tidy # 添加或者删除 module
go mod vendor # 将依赖复制到 vendor 目录下（这样 go build 的时候，go会优先从vendor目录寻找依赖包。）

go mod download # 下载module到本地
go get # 获取依赖到本地并添加到go.mod中
go build # 会拉取包之后然后进行编译
go clean -modcache # 清理本地Cache

# Go mod 打印依赖图
go mod graph

# Go mod 删除错误或者不使用的modules
go mod tidy

# Go mod 生成vendor目录
go mod vendor

# Go mod 验证依赖是否正确
go mod verify

# Go mod 查找依赖
go mod why

# GO mod 更新依赖到最新版本
go get -u github.com/golang/protobuf

# Go mod 更新到指定版本
go get -u github.com/golang/protobuf@指定版本
# Go mod 查看有哪些版本
go list -m -versions github.com/golang/protobuf

go mod edit  # 编辑 go.mod 文件，帮助查看 `go help mod edit`
go mod edit --require=geecache@v0.0.1 
# 替换包源 go mod edit --replace=old_module=new_module
go mod edit --replace=golang.org/x/sys@v0.0.0=github.com/golang/sys@latest
go mod edit --replace=geecache=./geecache

# Go打包给其他包调用
git tag -a v0.0.1 -m "Golang打包给其他包调用" && git push origin v0.0.1
go get -u xxxxxx
```

GO111MODULE=off：编译的时候会在$GOPATH/src目录下寻找

GO111MODULE=on：go依赖将不再GOPATH/src中寻找依赖，而是存在GOPATH/pkg/mod目录，多个项目可以共享缓存modules

GO111MODULE=auto：项目创建在$GOPATH/src之外才会开启go mod

module语句指定包的名字

require语句指定的依赖项模块

replace语句可以替换依赖项模块

exclude语句可以忽略依赖项模块

# 参考
- ★[Go 包管理详解 知乎专栏](https://zhuanlan.zhihu.com/p/92992277)
- [go build不从本地gopath获取_Go包管理GOPATH、vendor、go mod机制](https://blog.csdn.net/weixin_33788465/article/details/112098509)
- ★ [深入Go Module之go.mod文件解析](https://colobu.com/2021/06/28/dive-into-go-module-1/)
