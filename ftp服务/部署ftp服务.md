## 环境和工作模式

系统：Centos7.4

vsftpd认证模式：虚拟用户认证



## 安装vsftpd



检查是否已经安装ftp服务：

```bash
rpm -qa | grep vsftpd
```



安装ftp服务：

```bash
yum install -y vsftpd
```



vsftpd安装在`/etc/vsftpd`目录下，其中文件有：

- vsftpd.conf：核心配置文件；
- user_list：允许登录ftp的用户列表（白名单）；
- ftpuser：不能访问ftp的用户列表（黑名单）；



## 配置vsftpd



编辑`/etc/vsftpd/vsftpd.conf`文件，常用的参数如下：

- listen=YES：YES表示服务以独立方式运行，NO表示以xinetd方式运行；这里设置为YES；
- listen_ipv6=NO：是否监听IPV6，这里设置为NO；
- anonymous_enable=NO：是否开启匿名用户访问，这里设置为NO；
- anon_mkdir_write_enable=NO：是否允许匿名用户创建目录，这里设置为NO；
- anon_upload_enable=NO：是否允许匿名用户上传，这里设置为NO；
- local_enable=YES：是否允许本地用户登录，这里设置为YES；
- write_enable=YES：是否允开放本地用户写权限，这里设置为YES；
- chroot_local_user=YES：本地用户是否可执行chroot，这里设置为YES；
- chroot_list_enable=NO：是否只有指定用户才能执行chroot，这里设置为NO；
- chroot_list_file=<filename>：当chroot_local_user为NO且chroot_list_enable为YES，该文件中的用户才能chroot；
- ftpd_banner=xxxxx：客户端连接服务器后的欢迎信息；
- pam_service_name=vsftpd.vu：认证配置文件（文件名在下面设置的）；
- userlist_enable=YES：是否起用userlist_file白/黑名单用户列表，这里设置为YES；



还需要添加如下的参数：

- guest_enable=YES：开启虚拟用户模式；
- guest_username=virtual：虚拟用户对应的系统用户；
- allow_writeable_chroot=YES：允许对ftp根目录执行写操作；
- user_config_dir=/etc/vsftpd/vuser_dir：虚拟用户配置文件目录；
- pasv_min_port=45000：被动模式最小端口；
- pasv_max_port=49000：被动模式最大端口；

## 创建认证用户数文件



```bash
cat > /etc/vsftpd/user.txt << EOF
developer
123456
EOF
```



> 其中，奇数行为用户名，偶数行为密码



然后使用`db_load`将明文用户密码信息加密：

```bash
db_load -T -t hash -f /etc/vsftpd/user.txt /etc/vsftpd/user.db
chmod 600 /etc/vsftpd/user.db
```



创建虚拟用户和ftp根目录：

```bash
useradd -d /data/ftp-data -s /sbin/nologin virtual
chmod -Rf 755 /data/ftp-data
```





## 创建PAM文件

PAM文件用于支持虚拟用户。

```bash
cat > /etc/pam.d/vsftpd.vu << EOF
auth    required    pam_userdb.so    db=/etc/vsftpd/user
account    required    pam_userdb.so    db=/etc/vsftpd/user
EOF
```





## 为虚拟用户配置相关参数



创建存放用户的目录

```bash
mkdir /etc/vsftpd/vuser_dir
```



以用户名为文件名，创建和用户配置文件：

```bash
touch /etc/vsftpd/vuser_dir/developer
```



根据各用户的情况添加配置：

```bash
cat > /etc/vsftpd/vuser_dir/developer << EOF
local_root=/data/ftp-data/developer
anon_upload_enable=YES
anon_mkdir_write_enable=YES
anon_other_write_enable=YES
anon_world_readable_only=YES
EOF
```





## 启动服务

```bash
systemctl start vsftpd
systemctl status vsftpd
systemctl enable vsftpd
```





## 访问

通过浏览器访问21端口即可：

```
ftp://<ip地址>:21
```

