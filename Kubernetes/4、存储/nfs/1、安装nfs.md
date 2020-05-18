## 安装nfs

找一台服务器作为nfs机器，安装nfs服务：

```bash
yum install -y nfs-utils rpcbind
```



创建nfs目录：

```bash
mkdir -p /opt/nfs/data
echo "/opt/nfs/data *(rw,no_root_squash)" >> /etc/exports
```

> 生产上应该给该目录挂载一个数据盘



启动nfs：

```bash
systemctl start rpcbind
systemctl status rpcbind
systemctl enable rpcbind
systemctl start nfs 
systemctl status nfs
systemctl enable nfs
```

<br>



在每个node节点和master节点上，都要安装nfs-utils：

```bash
yum install -y nfs-utils
```



