# 持久化

参考链接：[JavaGuide](https://javaguide.cn/database/redis/redis-questions-01.html#redis-%E6%8C%81%E4%B9%85%E5%8C%96%E6%9C%BA%E5%88%B6)、[博客园](https://www.cnblogs.com/ysocean/p/9114268.html)、[博客园](https://www.cnblogs.com/ysocean/p/9114267.html)、[华为云](https://bbs.huaweicloud.com/blogs/detail/302470)

## 怎么保证 Redis 挂掉之后再重启数据可以进行恢复？

很多时候我们需要**持久化数据也就是将内存中的数据写入到硬盘**里面，大部分原因是为了之后重用数据（比如重启机器、机器故障之后恢复数据），或者是为了防止系统故障而将数据备份到一个远程位置。

Redis 不同于 Memcached 的很重要一点就是，Redis 支持持久化，而且支持两种不同的持久化操作。一种方式是**快照（Redis Database，RDB）**，另一种方式是**只追加文件（Append Only File，AOF）**。

## RDB方式

### 简介

RDB是Redis用来进行持久化的一种方式，是把**当前内存中的数据集快照写入磁盘**，也就是 **Snapshot 快照**（数据库中所有键值对数据）。恢复时是将快照文件直接读到内存里。

### 触发方式

RDB 有两种触发方式，分别是自动触发和手动触发。

#### 自动触发

`redis.conf`配置文件中默认配置为：

```
save 900 1
save 300 10
save 60 10000
```



#### 手动触发



## AOF方式

