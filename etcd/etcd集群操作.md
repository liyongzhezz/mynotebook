## 准备

在操作前，先设置好环境变量：

```bash
export ETCDCTL_API=3
HOST_1=10.240.0.17
HOST_2=10.240.0.18
HOST_3=10.240.0.19
ENDPOINTS=$HOST_1:2379,$HOST_2:2379,$HOST_3:2379
```

<br>



## 写入数据

```bash
etcdctl --endpoints=$ENDPOINTS put foo "Hello World!"
etcdctl --endpoints=$ENDPOINTS put web1 value1
etcdctl --endpoints=$ENDPOINTS put web2 value2
etcdctl --endpoints=$ENDPOINTS put web3 value3
```

<br>



## 读取数据

根据key读取数据：

```bash
etcdctl --endpoints=$ENDPOINTS --write-out="json" get foo
```



根据前缀读取数据：

```bash
etcdctl --endpoints=$ENDPOINTS get web --prefix
```

<br>



## 删除数据

根据key删除数据：

```bash
etcdctl --endpoints=$ENDPOINTS put key myvalue
etcdctl --endpoints=$ENDPOINTS del key
```



根据前缀删除数据：

```bash
etcdctl --endpoints=$ENDPOINTS put k1 value1
etcdctl --endpoints=$ENDPOINTS put k2 value2
etcdctl --endpoints=$ENDPOINTS del k --prefix
```

<br>



## Transactional write

`txn`指令将多个请求包装到同一个事物中：

```bash
etcdctl --endpoints=$ENDPOINTS put user1 bad
etcdctl --endpoints=$ENDPOINTS txn --interactive

compares:
value("user1") = "bad"      

success requests (get, put, delete):
del user1  

failure requests (get, put, delete):
put user1 good

etcdctl --endpoints=$ENDPOINTS get user1
```



<br>



## watch

`watch`可以监控特性发生的变更：

```bash
# 在第一个窗口
etcdctl --endpoints=$ENDPOINTS watch stock --prefix

# 在第二个窗口
etcdctl --endpoints=$ENDPOINTS put stock1 10
etcdctl --endpoints=$ENDPOINTS put stock2 20
```

<br>



## 设置key超时时间

etcd使用`lease`先创建租约，设定超时时间（租约时间），然后在创建key的时候绑定这个租约，实现key的超时（TTL）：

```bash
# 常见租约
$ etcdctl --endpoints=$ENDPOINTS lease grant 100
lease 38015a3c00490513 granted with TTL(100s)

# 创建key并绑定租约
$ etcdctl --endpoints=$ENDPOINTS put k1 v1 --lease=38015a3c00490513

# 查看租约有效期
$ etcdctl --endpoints=$ENDPOINTS lease timetolive 38015a3c00490513
lease 38015a3c00490513 granted with TTL(100s), remaining(67s)

$ etcdctl --endpoints=$ENDPOINTS lease timetolive 38015a3c00490513
lease 38015a3c00490513 granted with TTL(100s), remaining(64s)

$ etcdctl --endpoints=$ENDPOINTS lease timetolive 38015a3c00490513 --keys
lease 38015a3c00490513 granted with TTL(100s), remaining(59s), attached keys([k1])

$ etcdctl --endpoints=$ENDPOINTS put k2 v2 --lease=38015a3c00490513
OK
$ etcdctl --endpoints=$ENDPOINTS lease timetolive 38015a3c00490513 --keys
lease 38015a3c00490513 granted with TTL(100s), remaining(46s), attached keys([k1 k2])

# 等到时间到期
$ etcdctl --endpoints=$ENDPOINTS get k1

# 删除租约并删除关联的key
$ etcdctl --endpoints=$ENDPOINTS lease revoke 38015a3c00490513 
lease 38015a3c00490513 revoked
$ etcdctl --endpoints=$ENDPOINTS get k1
$ etcdctl --endpoints=$ENDPOINTS get k2

# 通过keepalived确保租约不过期
$ etcdctl --endpoints=$ENDPOINTS lease grant 10
lease 38015a3c0049051d granted with TTL(10s)

$ etcdctl --endpoints=$ENDPOINTS lease keep-alive 38015a3c0049051d
lease 38015a3c0049051d keepalived with TTL(10)
lease 38015a3c0049051d keepalived with TTL(10)
lease 38015a3c0049051d keepalived with TTL(10)
```

