# RabbitMQ 介绍

RabbitMQ是一个消息中间件:它接受并转发消息。你可以把它当做一个快递站点，当你要发送一个包裹时，你把你的包裹放到快递站，快递员最终会把你的快递送到收件人那里，按照这种逻辑RabbitMQ是一个快递站，一个快递员帮你传递快件。RabbitMQ与快递站的主要区别在于，它不处理快件而是接收，存储和转发消息数据。

# 1 安装

mac 通过homebrew 安装
`brew install rabbitmq`

其他平台可以使用安装包下载安装

mac 配置环境变量:

在.zshrc、.bashrc、.bash_profile 中其中一个文件添加如下内容，主要看自己的mac电脑的芯片架构,选择对应架构添加即可

```
# for macOS Intel
export PATH=$PATH:/usr/local/sbin
# for Apple Silicon
export PATH=$PATH:/opt/homebrew/sbin
```

名词介绍

**Broker**:接收和分发消息的应用，RabbitMQ Server就是Message Broker

**Connection**: publisher / consumer和 broker之间的TCP连接

**Channel**:如果每一次访问RabbitMQ都建立一个Connection，在消息量大的时候建立TCP
Connection的开销将是巨大的，效率也较低。Channel是在connection 内部建立的逻辑连接，如果应用程序支持多线程，通常每个thread创建单独的channel进行通讯，AMQP method包含了channel id 帮助客户端和message broker识别 channel，所以channel 之间是完全隔离的。Channel作为轻量级的Connection极大减少了操作系统建TCP connection的开销

**Exchange**: message 到达 broker 的第一站，根据分发规则，匹配查询表中的 routing key，分发消息到queue 中去。常用的类型有: direct (point-to-point), topic(publish-subscribe) and fanout
(multicast)

**Routing Key**:生产者将消息发送到交换机时会携带一个key,来指定路由规则

**binding Key**:在绑定Exchange和Queue时，会指定一个BindingKey,生产者发送消息携带的RoutingKey会和bindingKey对比，若一致就将消息分发至这个队列

**vHost** 虚拟主机：每一个RabbitMQ服务器可以开设多个虚拟主机每一个vhost本质上是一个mini版的RabbitMQ服务器，拥有自己的 "交换机exchange、绑定Binding、队列Queue"，更重要的是每一个vhost拥有独立的权限机制，这样就能安全地使用一个RabbitMQ服务器来服务多个应用程序，其中每个vhost服务一个应用程序。

# 1 工作模式

1. simple 简单模式:  一个消费者消费一个生产者生产的信息

![rabbit_single.png](../../assets/6548f298f2efc3498ad1eab9776ae7c76fe8e25d.png)

   

2. work queues 工作模式: 一个生产者生产信息，多个消费者进行消费，但是一条消息只能消费一次
    利用该特点，在消息积压较多的时候，可以考虑开启多个消费端来处理
   
   ![WechatIMG26.png](../../assets/eeb8b1475b0e8a4a15fac1f61cc5172261e33e56.png)

3. Publish/Subscribe 发布订阅模式: 生产者首先投递消息到交换机，订阅了这个交换机的队列就会收到生产者投递的消息
   
    消息被路由投递给多个队列，一个消息被多个消费者获取，订阅模式需要设置exchange(交换机)
   
   
   ![WechatIMG27.png](../../assets/65bbcd3349cca3ae585fb77c9deaaba895c0710f.png)

4. Routing 路由模式: 生产者生产消息投递到direct交换机中，扇出交换机会根据消息携带的routing Key匹配相应的队列
   
    一个消息被多个消费者获取，并且消息的目标队列可被生产者指定

![WechatIMG28.png](../../assets/0cd448ee178aefc30835ada5de8a8ccc484b80f6.png)



5. Topics 主题模式:  生产者生产消息投递到topic交换机中，上面是完全匹配路由键，而主题模式是模糊匹配，只要有合适规则的路由就会投递给消费者
   
    一个消息被多个消费者获取。消息的目的queue可以用 BindingKey 以通配符，（#: 一个或多个词， *: 一个词）的方式指定
   
    主题模式跟路由模式最大的区别:
   
        1. 消息分发的匹配规则从原来的 ***确定匹配*** 变成了 **通配匹配**
        2. exchange type 变成了 topic
   
   ![WechatIMG29.png](../../assets/754d0873f8ad30d3fa5680cbd0278f44c6dda9d4.png)
   
   

# 2 常用命令

**linux**

```
systemctl start rabbitmq-server # 启动
rabbitmqctl stop # 关闭
```

**mac**

```
brew services start rabbitmq #brew启动
brew services stop rabbitmq #brew启动
brew services restart rabbitmq #brew启动

rabbitmq-server #前台运行
rabbitmq-server -detached  # 后台运行
rabbitmqctl add_user 账号 密码  #新建用户
rabbitmqctl set_user_tags 账号 administrator #给用户分配操作权限

#修改密码
rabbitmqctl change_password Username Newpassword 修改密码

#删除用户
rabbitmqctl delete_user Username 删除用户

#查看所有用户
rabbitmqctl list_users 查看用户清单

#为用户设置访问权限
rabbitmqctl set_permissions -p / 用户名 ".*" ".*" ".*" 
rabbitmqctl set_permissions -p / root ".*" ".*" ".*"

# 查找rabbitmq在哪
find / -name 'rabbitmq' -type d

# rabbitmq内存限制
# 系数计算，假设机器内存32g,以下命令限制的最高内存为，32*0.06=1.92g
rabbitmqctl set_vm_memory_high_watermark 0.06

# 列出插件
rabbitmq-plugins list

# rabbitmq添加消息日志记录
rabbitmq-plugins enable rabbitmq_tracing
# 卸载插件
rabbitmq-plugins enable rabbitmq_tracing

# 查询rabbitmq的进程
ps -ef | grep rabbitmq

# 杀掉rabbitmq进程
ps -ef | grep rabbitmq | grep -v grep | awk '{print $2}' | xargs kill -9

# 查询rabbitmq的状态
sudo rabbitmqctl status
```

# 常用操作

1. 启动后 登录web控制台， RabbitMQ 控制台的默认端口15672,默认账号密码都是guest
   
    http://localhost:15672
   
    连接服务的默认端口是5672
   
    "amqp://malluser:123456@localhost:5672/mallhost"

[RabbitMQ 官网](https://www.rabbitmq.com/download.html)
[Mac 安装](https://zhuanlan.zhihu.com/p/555761839)