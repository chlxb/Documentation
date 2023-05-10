# docker

## 1. 安装: 
1. 安装包安装
2. brew 安装（mac）:

    `brew install --cask --appdir=/Applications docker`

### 1.1配置文件
linux: `/etc/docker/daemon.json`

mac: `$HOME/.docker/daemon.json`

### 中央仓库


### 镜像

### 常用命令
1. docker image
```
docker search imagename // 查找镜像
docker pull imagename:version // 拉取官方仓库的镜像
docker commit // 提交一个镜像
docker images // 列出本地的所有镜像
docker save -o path filename imageID/imagename // 到出镜像
docker load -i filename // 导入镜像
docker rmi imageID/imagename // 删除镜像
docker tag iamgeID 新镜像名称：版本
```


## 2 dockerfile


## 3 Docker compose
Docker Compose 用来进行多容器控制，具体可以做到以下几点：
* 提供工具用于定义和运行多个docker容器应用；
* 使用yaml文件来配置应用服务(docker-compse.yml)；
* 可以通过一个简单的命令docker-compse up可以按照依赖关系启动所有服务；
* 可以通过一个简单的命令docker-compose down停止所有服务；
* 当一个服务需要的时候，可以很简单地通过--scale进行扩容；

Docker Compose有以下特征:
* 更高的可移植性，Docker Compose仅需一个docker-compse up可以完成按照依赖关系启动所有服务，然后使用docker-compose down轻松将其拆解。帮助我们更轻松地部署复杂的应用程序；
* 单个主机上的多个隔离环境，Compose可以使用项目名称将环境彼此隔离，这带可以在一台计算机上运行同一环境的多个副本，它可以防止不同的项目和服务相互干扰；

# 参考
----
* [Docker 官网](https://docs.docker.com)
* [Docker 百度](https://baijiahao.baidu.com/s?id=1708726749585822590&wfr=spider&for=pc)
* [Docker compose](https://docs.docker.com/compose/install/)
* [Docker 教程 - csdn](https://blog.csdn.net/m0_67402341/article/details/124526419)
* [菜鸟教程](https://www.runoob.com/docker/docker-image-usage.html)
