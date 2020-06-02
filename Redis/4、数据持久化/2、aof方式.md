## AOF方式

将以日志的方式记录服务器处理的每一次操作。服务器启动之初会读取日志文件来重建数据库。

<br>



## 优势和缺陷

优势：

- 更高的数据安全性；
- 不会破坏日志文件的数据完整性；
- redis可以自动启动日志重写机制，对重复的日志进行合并，减小文件大小；
- 包含格式清晰的日志文件。可以基于这个日志文件进行数据重建和迁移；
- AOF将数据库操作顺序写到文件的最后，如果出现误操作，可以在文件中删掉最后一行在进行还原；



劣势：

- 对于相同的数据集来说，AOF往往比RDB文件大；
- 效率低于RDB；

<br>



## 配置AOF持久化

在redis.conf配置文件中，有如下的一些关于AOF持久化的配置：

```bash
// AOF持久化功能开关，默认没有打开，改成yes表示打开
appendonly yes

// AOF文件名
appendfilename "appendonly.aof"

// AOF同步策略设置
// 每修改一次同步到磁盘上
appendfsync always

// 每秒同步一次到磁盘上
appendfsync everysec

// 不同步
appendfsync no
```

<br>



## 同时使用RDB和AOF需要注意

在同时使用AOF和RDB持久化数据的时候，重启redis后，优先使用AOF进行数据恢复。

