## 多数据库特性

一个redis实例可以包含多个数据库，一个客户端可以指定连接redis实例其中的一个数据库。

> 一个redis实例可以提供16个数据库，编号从0 到 15。客户端默认是连接0号数据库。



### 连接指定的数据库

可以通过select指令加上数据库编号来连接数据库，例如：

```bash
127.0.0.1:6379> select 1
OK

127.0.0.1:6379[1]> keys *
(empty list or set)

127.0.0.1:6379[1]> select 0
OK

127.0.0.1:6379> keys *
 1) "mya3"
 2) "mya1"
 3) "qq"
 ...
```





### 数据库间移动key

可以将一个key从一个库移动到另一个库，使用move指令，例如：

```bash
// 将0库中的myset这个key移动到1库中
127.0.0.1:6379> move myset 1
(integer) 1

127.0.0.1:6379> select 1
OK

127.0.0.1:6379[1]> keys *
1) "myset"
```

<br>



## 事务特性

事务执行将被串行执行，执行期间redis将不会为其他客户端提供服务，从而保证事务中的指令原子化执行。redis中实现事务特性使用multi、exec、discard指令。

- multi：将会创建一个事务，其后的参数将被视为事务中的命令；
- exec：exec将会执行事务；
- discard：事务回滚



如果在exec之前出现网络问题连接不上redis，则事务中的命令不会被执行；如果exec之后出现网络问题连接不上redis，则事务将继续执行。

### multi

multi将会开启一个事务，其后的指令将会存在事务命令队列中，直到执行。

```bash
127.0.0.1:6379> set num 2
OK

127.0.0.1:6379> get num
"2"

127.0.0.1:6379> incr num
QUEUED

127.0.0.1:6379> incr num
QUEUED
```



>  可以看到，事务命令已经被存在队列中。



### exec

exec将会执行事务命令队列中的命令，相当于mysql中的commit。

```bash
127.0.0.1:6379> exec 
1) (integer) 3
2) (integer) 4
```



### discard

discard是回滚操作，相当于mysql中的rollback。

```bash
127.0.0.1:6379> set user tom
OK
127.0.0.1:6379> get user
"tom"
127.0.0.1:6379> multi
OK
127.0.0.1:6379> set user jerry
QUEUED
127.0.0.1:6379> discard
OK
127.0.0.1:6379> get user
"tom"
```

