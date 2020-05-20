## 部署服务

```bash
yum install -y vsftpd*
```



启动服务：

```bash
systemctl start vsftpd
systemctl status vsftpd
systemctl enable vsftpd
```



检查服务启动情况：

```bash
ps -ef | grep vsftpd
netstat -ntlp | grep 21
```



<br>



## 修改配置文件允许匿名用户上传文件

配置文件地址为`/etc/vsftpd/vsftpd.conf`，修改参数为如下的值：

```bash
anon_upload_enable=YES                       #允许匿名用户上传
anon_mkdir_write_enable=YES               #允许匿名用户创建目录
```



重启服务：

```bash
systemctl restart vsftpd
```

<br>



## 配置虚拟用户方式登录

虚拟用户比匿名用户更安全。



### 创建系统用户

创建系统用户并禁止登录系统：

```bash
useradd vsftpd -d /home/vsftpd -s /bin/false
```



### 创建虚拟用户主目录

后续该虚拟用户文件都放在这个下面：

```bash
mkdir -p /home/vsftpd/ftp1
```



### 指定虚拟用户信息

```bash
# 创建了ftp1这个虚拟用户，密码为123456
cat > /etc/vsftpd/loginusers.conf  << EOF
ftp1
123456
EOF
```



### 创建数据库文件

```bash
db_load -T -t hash -f /etc/vsftpd/loginusers.conf /etc/vsftpd/loginusers.db
chmod 600 /etc/vsftpd/loginusers.db
```



```bash
# 注释掉原来所有内容后，增加下面的内容
vim /etc/pam.d/vsftpd
auth    sufficient /lib64/security/pam_userdb.so db=/etc/vsftpd/loginusers
account sufficient /lib64/security/pam_userdb.so db=/etc/vsftpd/loginusers
```



### 增加虚拟用户数据库配置文件

```bash
# 创建虚拟用户配置文件目录
mkdir /etc/vsftpd/userconf         

# 这里的文件名必须与前面指定的虚拟用户名一致
vim /etc/vsftpd/userconf/ftp1  
local_root=/home/vsftpd/ftp1/write_enable=YES
```



### 修改主配置文件

```bash
# 存在的修改，不存在的增加
vim   /etc/vsftpd/vsftpd.conf    
anonymous_enable=NO           # 禁止匿名用户登录
chroot_local_user=YES         # 禁止用户访问除主目录以外的目录
ascii_upload_enable=YES       # 设定支持ASCII模式的上传和下载功能   
ascii_download_enable=YES     # 设定支持ASCII模式的上传和下载功能   
guest_enable=YES              # 启动虚拟用户
guest_username=vsftpd         # 虚拟用户使用的系统用户名
user_config_dir=/etc/vsftpd/userconf   # 虚拟用户使用的配置文件目录
allow_writeable_chroot=YES    # 最新版的vsftpd为了安全必须用户主目录（也就是/home/vsftpd/ftp1）没有写权限，才能登录
```



### 重启服务

```bash
systemctl restart vsftpd
```



### 访问

用户访问ftp服务，使用用户`ftp1`，密码：`123456`即可登录，实际对应的是使用系统用户`vsfptd`



<br>

