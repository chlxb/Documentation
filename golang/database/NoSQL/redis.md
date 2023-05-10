# redis

redis是一个key-value存储系统。和Memcached类似，它支持存储的value类型相对更多，包括string(字符串)、list(链表)、set(集合)和zset(有序集合)。

## 驱动

Go目前支持redis的驱动有如下:

- https://github.com/gomodule/redigo (推荐)
- https://github.com/go-redis/redis
- https://github.com/hoisie/redis
- https://github.com/alphazero/Go-Redis
- https://github.com/simonz05/godis

以 redigo 驱动来演示

## 常用命令

终端连接 docker 的 redis

```
// docker 启动 redis
docker run -itd -p 6379:6379 --name redis -v ~/docker/redis/redis_conf/redis.conf:/etc/redis/redis.conf -v ~/docker/redis/data/:/redis/data redis redis-server  --appendonly yes

// docker exec -ti {容器id} redis-cli -h {虚拟机地址} -p {端口号/默认6379}
docker exec -ti 1041bb02242d redis-cli -h 127.0.0.1 -p 6379
```

# 参考

----

[redigo 文档](https://pkg.go.dev/github.com/gomodule/redigo/redis#String)
[redigo github](https://github.com/gomodule/redigo)
[菜鸟教程](https://www.runoob.com/redis/redis-install.html)
[mac 安装redis](https://juejin.cn/post/7099771580812623902)
[连接docker 中的redis](https://blog.csdn.net/Milogenius/article/details/105854009)
[docker redis 连接失败分析](https://www.cnblogs.com/magel/p/16312801.html)
[docker 安装mysql和redis](https://zhuanlan.zhihu.com/p/485342342)
[docker redis 安装](https://blog.csdn.net/Lcy_990227/article/details/109725694)

[可视化工具：AnotherRedisDesktopManager](https://github.com/qishibo/AnotherRedisDesktopManager)
[可视化工具：QuickRedis](https://gitee.com/quick123official/quick_redis_blog/)
[可视化工具：Redis Desktop Manager](https://resp.app)