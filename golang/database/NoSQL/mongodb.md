# mongoDB
MongoDB是一个高性能，开源，无模式的文档型数据库，是一个介于关系数据库和非关系数据库之间的产品，是非关系数据库当中功能最丰富，最像关系数据库的。他支持的数据结构非常松散，采用的是类似json的bjson格式来存储数据，因此可以存储比较复杂的数据类型。
Mongo最大的特点是他支持的查询语言非常强大，其语法有点类似于面向对象的查询语言，几乎可以实现类似关系数据库单表查询的绝大部分功能，而且还支持对数据建立索引。

## 驱动
目前Go支持mongoDB最好的驱动就是mgo，这个驱动目前最有可能成为官方的pkg。

`go get gopkg.in/mgo.v2`


## 本地 mongodb
### 安装
1. 普通命令安装
```
// 安装在 /usr/local 路径下
// step 1 下载
sudo curl -O https://fastdl.mongodb.org/osx/mongodb-osx-ssl-x86_64-4.0.9.tgz
// step 2 解压
sudo tar -zxvf mongodb-osx-ssl-x86_64-4.0.9.tgz
// step 3 
sudo mv mongodb-osx-x86_64-4.0.9/ /usr/local/mongodb
// step 4 path配置
export PATH=/usr/local/mongodb/bin:$PATH
```
2. homebrew 安装
   
    安装信息：
    - 配置文件：/usr/local/etc/mongod.conf
    - 日志文件路径：/usr/local/var/log/mongodb
    - 数据存放路径：/usr/local/var/mongodb
```
brew tap mongodb/brew
brew install mongodb-community@4.4
// brew 启动
brew services start mongodb-community@4.4
// brew 停止
brew services stop mongodb-community@4.4
```

### 配置文件
可以通过配置文件来启动 mongodb， 配置文件可以是 yaml 文件格式配置:
```
processManagement:
   fork: true
net:
  bindIp: 127.0.0.1
  port: 27017
storage:
  dbPath: /etc/mongodb/data
  journal:
    enabled: true
systemLog:
  destination: file
  path: "/etc/mongodb/logs/mongod.log"
  logAppend: true
```

也可以使用 mongod 和 mongos 命令行设置格式 INI-style (推荐使用yaml格式的，新版本不支持解析这种格式):
```
dbpath=/usr/local/mongodb/data
logpath=/usr/local/mongodb/log/mongodb.log
logappend = true
port=27017
#fork=true
auth=true
bind_ip = 127.0.0.1
journal=true
quiet=true
```

### 命令行
```
// --dbpath 数据库路径
// --logpath 日志文件路径
// --fork 后台运行服务
mongod --dbpath ./mongodb --logpath ./mongodb/log/mongo.log --fork
// 使用指定配置文件启动服务
mongod -f /usr/local/mongodb/config/mongodb.conf
// 查看 mongodb 服务是否启动
ps aux | grep -v grep | grep mongod
// 关闭
db.adminCommand({ "shutdown" : 1 })
```


## docker 
1. 拉取镜像
```
docker pull mongo 
```

2. 启动容器
```
// 创建并启动容器。没有容器的时候这么开始
// -p 27017:27017 ：映射容器服务的 27017 端口到宿主机的 27017 端口。外部可以直接通过 宿主机 ip:27017 访问到 mongo 的服务。
// –auth：需要密码才能访问容器服务。
// --name mongodb 创建 名称为 mongodb 的容器
// 第二个 mongodb 是 image 的名字
// docker run -itd --name mongodb -p 27017:27017 mongodb --auth

// 或 run -d --name mongodb -p 27017:27017  [imageID]
docker run -d --name mongodb -p 27017:27017 01aae53d5b7427eedc2e6490617acb0f72fe0b5776bd67041c6fc01daaa66203

// 或通过配置文件启动
docker run -itd -p 27017:27017 --name mongodb -v ~/docker/mongodb:/etc/mongodb -d mongodb --config /etc/mongodb/config/mongodb.conf 

// 启动容器 有容器之后这么启动
docker start mongodb
```

3. 创建用户
```
// docker exec -it mongo mongosh [username]
docker exec -it mongo mongosh root
// 切换到 admin 数据库 这个是默认创建的数据库
root>use admin 
// 创建 root 用户 密码 123456 
root>db.createUser({ user:'root', pwd:'123456', roles:[{role:'userAdminAnyDatabase', db:'admin'}, "readWriteAnyDatabase"]});
// 验证用户
root>db.auth('root', '123456')
```

1. 创建数据库,语法:`use [database_name]`
如果数据库不存在，则创建数据库，否则切换到指定数据库
```
// 创建 或 切换 数据库 
use test
// 显示数据库
show dbs
```



## 参考
----
[mgo 驱动官网](http://labix.org/mgo)
[mgo github](https://github.com/go-mgo/mgo/tree/v2)
[docker 配置mongodb](https://blog.csdn.net/liuyunshengsir/article/details/127924865)
[docker configuration官网文档](https://www.mongodb.com/docs/manual/reference/configuration-file-settings-command-line-options-mapping/)
[mongodb 菜鸟教程](https://www.runoob.com/mongodb/mongodb-osx-install.html)
[mongodb 配置文件](https://blog.csdn.net/chj_1224365967/article/details/120967002)
[MongoDB Hub](https://hub.docker.com/_/mongo)
[MongoDB 官网](https://www.mongodb.com/docs/manual/introduction/)




