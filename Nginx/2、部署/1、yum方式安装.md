## 安装依赖

```bash
yum install -y gcc gcc-c++ autoconf pcre pcre-devel make automake yum-utils zlib zlib-devel openssl openssl-devel
```



 有些工具包也可以选择安装：

```bash
yum install -y wget http-tools vim
```

<br>‌

## 添加yum源

```bash
cat > /etc/yum.repos.d/nginx.repo << EOF
[nginx-stable]
name=nginx stable repo
baseurl=http://nginx.org/packages/centos/\$releasever/\$basearch/
gpgcheck=1
enabled=1
gpgkey=https://nginx.org/keys/nginx_signing.key
module_hotfixes=true


[nginx-mainline]
name=nginx mainline repo
baseurl=http://nginx.org/packages/mainline/centos/\$releasever/\$basearch/
gpgcheck=1
enabled=0
gpgkey=https://nginx.org/keys/nginx_signing.key
module_hotfixes=true
EOF
```

<br>

‌

## 安装nginx

```bash
# 查看nginx版本
yum list | grep nginx


# 安装最新版
yum install -y nginx
```

<br>



## 查看nginx版本和编译参数

```bash
# 查看nginx版本
nginx -v


# 查看nginx编译参数
nginx -V
```

<br>



## 默认安装的目录和文件

使用下面的命令可以查看nginx通过rpm方式安装的目录结构和文件：



```bash
rpm -ql nginx
```



重要的配置文件如下：

- `/etc/logrotate.d/nginx`：nginx配置logrotate日志切割的配置文件；
- `/etc/nginx`：安装目录；
- `/etc/nginx/nginx.conf`：主配置文件；
- `/etc/nginx/conf.d`：其他配置存放的目录；
- `/etc/nginx/conf.d/default.conf`：默认加载的配置；
- `/etc/nginx/fastcgi_params`：fastcgi配置文件；
- `/etc/nginx/scgi_params`：scgi配置文件；
- `/etc/nginx/uwsgi_params`：uwsgi配置文件；
- `/etc/nginx/{koi-win, koi-utf, win-utf}`：编码转换配置文件；
- `/etc/nginx/mime.types`：设置http协议的Content-Type与扩展名对应关系的配置文件；
- `/var/cache/nginx`：用于缓存的目录；
- `/var/log/nginx`：日志目录；

<br>



## nginx启动

```bash
# 启动
systemctl start nginx


# 开机自启动
systemctl enable nginx


# 查看nginx状态
systemctl status nginx


# 停止nginx
systemctl stop nginx


# 重启
systemctl restart nginx
```

