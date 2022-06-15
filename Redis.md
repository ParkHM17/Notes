# 持久化

参考链接：[JavaGuide](https://javaguide.cn/database/redis/redis-questions-01.html#redis-%E6%8C%81%E4%B9%85%E5%8C%96%E6%9C%BA%E5%88%B6)、[博客园](https://www.cnblogs.com/ysocean/p/9114268.html)、[博客园](https://www.cnblogs.com/ysocean/p/9114267.html)、[华为云](https://bbs.huaweicloud.com/blogs/detail/302470)

## 怎么保证 Redis 挂掉之后再重启数据可以进行恢复？

很多时候我们需要**持久化数据也就是将内存中的数据写入到硬盘**里面，大部分原因是为了之后重用数据（比如重启机器、机器故障之后恢复数据），或者是为了防止系统故障而将数据备份到一个远程位置。

Redis 不同于 Memcached 的很重要一点就是，Redis 支持持久化，而且支持两种不同的持久化操作。一种方式是**快照（Redis Database，RDB）**，另一种方式是**只追加文件（Append Only File，AOF）**。

## RDB

### 简介

RDB持久化方式能够在**指定的时间间隔能对你的数据进行快照存储**。

在默认情况下，Redis将数据库快照保存在名字为`dump.rdb`的二进制文件中。在Redis运行时，RDB将**当前内存中的数据库快照保存到磁盘文件**中，在Redis重启动时，RDB可以通过载入`.rdb`文件来还原数据库的状态。

![RDB](Redis.assets/RDB.png)

当Redis需要保存`dump.rdb`文件时，服务器执行以下操作：

1. Redis调用`forks()`，同时拥有父进程和子进程。
2. 子进程将数据集写入到一个临时`.rdb`文件中。
3. 当子进程完成对新`.rdb`文件的写入时，Redis用新`.rdb`文件替换原来的`.rdb`文件。

这种工作方式使得Redis可以从**写时复制（copy-on-write）**机制中获益。

### 触发机制

#### `save`命令（同步机制）

`save`命令执行一个同步操作，以`.rdb`文件的方式保存所有数据的快照。

```shell
127.0.0.1:6379>save
OK
```

![save](Redis.assets/redis-save-info.png)

由于`save`命令是**同步命令**，会占用Redis的主进程。若Redis数据非常多时，`save`命令执行速度会非常慢，**阻塞**所有客户端的请求。因此很少在生产环境直接使用`save`命令，可以使用`bgsave`命令代替。如果在`bgsave`命令的保存数据的子进程发生错误，用`save`命令保存最新的数据是最后的手段。

![save阻塞](Redis.assets/redis-save-block.png)

#### `bgsave`命令（异步机制）





















RDB 有两种触发方式，分别是自动触发和手动触发。

#### 自动触发

`redis.conf`配置文件中默认配置为：

```
save 900 1
save 300 10
save 60 10000
```

`save m n ` 表示m秒内数据集存在n次修改时，自动触发`bgsave`（看下文）。

除了默认配置外，还可添加其他配置信息：

```
dbfilename dump-<port>.rdb
dir /var/lib/redis
stop-writes-on-bgsave-error yes
rdbcompression yes
rdbchecksum yes
```

- `dbfilename`：设置快照的文件名，默认是 `dump.rdb`。
- `dir`：设置快照文件的存放路径。默认是和当前配置文件保存在同一目录。
- `stop-writes-on-bgsave-error`：默认值为`yes`。当启用了RDB且最后一次后台保存数据失败，Redis是否停止接收数据。
- `rdbcompression`：默认值是`yes`。对于存储到磁盘中的快照，可以设置是否进行压缩存储。如果是的话Redis会采用**LZF**算法进行压缩。
- `rdbchecksum`：默认值是`yes`。在存储快照后，我们还可以让Redis使用**CRC64**算法来进行数据校验。

在配置文件中配置`save`，当实际操作满足该配置形式时就会进行RDB持久化，将当前的内存快照保存在`dir`配置的目录中，文件名由配置的`dbfilename`决定。

如果**只使用Redis的缓存功能，不需要持久化**，那么可以注释掉所有的`save`行或直接添加一个空字符串来实现停用：

```
save ""
```

注意：

#### 手动触发



## AOF

