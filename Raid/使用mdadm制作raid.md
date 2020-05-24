通过内核`md`模块可以将独立的磁盘做成软raid，使用的是`mdadm`命令。



<br>



## 磁盘分区

将磁盘分为3个区进行测试，分区情况如下：

| Device Boot |  Start  |   End   | Blocks  |  Id  |        System         |
| :---------: | :-----: | :-----: | :-----: | :--: | :-------------------: |
|  /dev/vdb1  |  2048   | 2099199 | 1048576 |  fd  | Linux raid autodetect |
|  /dev/vdb2  | 2099200 | 4196351 | 1048576 |  fd  | Linux raid autodetect |
|  /dev/vdb3  | 4196352 | 6293503 | 1048576 |  fd  | Linux raid autodetect |



<br>



## 格式化

```bash
mkfs.xfs /dev/vdb1
mkfs.xfs /dev/vdb2
mkfs.xfs /dev/vdb3
```

<br>



## 创建raid

```bash
mdadm -C /dev/md0 -a yes -l 1 -n 2 -x 1 /dev/vdb{1..3}
```



- `-C`：表示创建，后面接创建的名称；
- `-a`：后面接yes表示如果md0不存在就自动创建；
- `-l`：raid级别，这里使用raid1；
- `-n`：指定raid使用的设别个数，这里使用两个；
- `-x`：表示空闲设别个数；

<br>



## 测试raid

```bash
# 挂载raid
mount /dev/md0 /data

# 查看raid
mdadm -D /dev/md0

# 损坏一个raid组件
mdadm -f /dev/md0 /dev/vdb1

# 删除损坏的raid组件
mdadm -r /dev/md0 /dev/vdb1

# 加入新的raid组件
mdadm -a /dev/md0 /dev/vdb1

# 解除挂载
umount /dev/md0

# 停止raid
mdadm -S /dev/md0
```



> 损坏raid组件后，闲置的组件就会顶替失效的组件工作；
>
> 加入的新的raid组件会放到闲置的组件中去；



<br>



