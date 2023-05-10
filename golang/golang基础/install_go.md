# go 环境安装
## linux
1. 下载 go 安装包：

`wget https://dl.google.com/go/go1.19.4.linux-amd64.tar.gz`

2. 解压到 /usr/local

`sudo tar -C /usr/local -xzf go1.4.2.linux-amd64.tar.gz`

3. 环境变量配置
在 .bashrc 文件末尾增加 gopath 的环境变量:
```
 # 按照本机配置的 gopath 路径设置
 export GOPATTH=/vagrant/gopath 
 export PATH=$PATH:/usr/local/go/bin:$GOPATH/bin
```
保存 .bashrc 文件，并使用 source 命令使配置生效:

`source .bashrc`

4. 使用 `go env` 命令验证是否配置成功