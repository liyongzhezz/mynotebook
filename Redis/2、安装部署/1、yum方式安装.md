# 依赖安装



```bash
yum install -y epel-release
```



直接yum安装的redis不是最新的版本，如果要安装最新版本需要执行下面的步骤：



```bash
yum install -y http://rpms.famillecollet.com/enterprise/remi-release-7.rpm
yum --enablerepo=remi install redis -y
```

<br>



#  安装Redis



```bash
yum install -y redis
redis-cli --version
```

<br>



#  设置配置文件

修改`/etc/redis.conf`中的`bind`参数为下面的值，开启允许远程登录：



```bash
bind 0.0.0.0
```

<br>



#  设置系统参数



```bash
sysctl vm.overcommit_memory=1
echo "sysctl vm.overcommit_memory=1" >> /etc/rc.local
```



`vm.overcommit_memory`是控制内存分配策略的参数：

- 1：内核分配所有的物理内存而不管当前内存状态；
- 0：内核检查是否有足够的内存共当前进程使用，没有则会返回错误给进程；
- 2：内核允许分配超过物理内存和交换空间总和的内存；

<br>



# 启动Redis



```bash
systemctl start redis
systemctl enable redis
systemctl status redis
```

