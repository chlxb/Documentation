## go module

1. 使用 go module管理项目依赖的时候，同一个目录下报错：
   `gopls was not able to find modules in your workspace`,解决方法：
   a. vscode开发则打开 setting.json -> 扩展 -> Go -> “在settings.json 中编辑” 添加如下内容:
   
   ```
   "gopls": {
    "build.experimentalWorkspaceModule": true
   },
   ```
   
   b. 生成 go.work 文件

参考：
[go.work](https://blog.csdn.net/wsi__/article/details/127348785)
[settings.json修改1](https://blog.csdn.net/weixin_46047387/article/details/127432122)
[settings.json修改1](https://www.cnblogs.com/ngrhl/p/15878099.html)

## go 命令

1. go run：`go run [build flags] [-exec xprog] package [arguments...]`

终端运行的时候 如果 package main 包里有 main.go 以外的文件，运行时要连同main.go一起运行。否则会提示找不到调用的方法, 
文件结构如下：

> -- project
> 
> > main.go
> > 
> > serve.go
> > 
> > form_client.go
> > 
> > package1
> > 
> > > package1.go
> > > 
> > > package2.go

    命令1：
        go run main.go serve.go form_client.go -client=true
    或 Unix* 用：
        go run . -client.go
    或 windows 用：
        go run ./ -client.go

2. go vet 命令: golang 中自带的静态分析工具
   
   > go vet xxx.go