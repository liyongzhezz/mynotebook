## 自签证书

```bash
# 确认安装了openssl
openssl version

# 确定nginx安装了https模块(应该有--with-http_ssl_module)
nginx -V

# 创建整数目录
cd /etc/nginx/
mkdir ssl
cd ssl

# 生成秘钥
$ openssl genrsa -out nginx.key 2048
Generating RSA private key, 2048 bit long modulus
..............................................+++
.....................................................................+++
e is 65537 (0x10001)

# 生成签名请求文件（csr），输入上边的密码，并输入相关的信息
openssl req -new -key nginx.key -out nginx.csr

# 也可以在一行，不用交互式输入
openssl req -subj "/C=CN/ST=Guangdong/L=Shenzhen/O=nginx/OU=dev/CN=test.example.com/emailAddress=test@123.com" -new -key nginx.key -out nginx.csr

# 生成自签名证书，指定过期时间3650天，输入密码即可生成
openssl x509 -req -days 3650 -in nginx.csr -signkey nginx.key -out nginx.crt

# 查看生成的文件
$ ls
nginx.crt  nginx.csr  nginx.key
```

<br>



## https配置语法

```nginx
ssl on | off;
# 是否开启ssl，默认为off；可以配置在http、server下

ssl_certificate <file>;
# ssl证书文件，可以配置在http、server下；

ssl_certificate_key_file <file>;
# ssl证书秘钥文件，可以配置在http、server下；
```



<br>



## 实例

```nginx
server {
  listen 443 ssl;
  server_name localhost;

  ssl_certificate /etc/nginx/ssl/nginx.crt;
  ssl_certificate_key /etc/nginx/ssl/nginx.key;

  index index.html index.htm;

  location / {
    root /usr/share/nginx/html;
  }
}
```



<br>



## 实例--苹果要求的https配置

苹果要求的https配置：

- 服务器所有连接使用TLS1.2以上（openssl1.0.2）；
- https证书使用SHA256以上的哈希算法签名；
- https证书使用RSA 2048位或ECC 256位以上公钥算法；
- 使用前向加密技术；



首先查看openssl版本：

```bash
$ openssl version
OpenSSL 1.0.2k-fips  26 Jan 2017
```



升级openssl可以使用如下的命令：

```bash
wget https://www.openssl.org/source/openssl-1.0.2k.tar.gz
tar zxf openssl-1.0.2k.tar.gz
cd openssl-1.0.2k
./configure --prefix=/usr/local/openssl
make && make install 
mv /usr/bin/openssl  /usr/bin/openssl.old
mv /usr/include/openssl /usr/include/openssl.old
ln -s /usr/local/openssl/bin/openssl /usr/bin/openssl
ln -s /usr/local/openssl/include/openssl /usr/include/openssl
echo "/usr/local/openssl/lib" >> /etc/ld.so.conf
ldconfig -v
openssl version -a
```



创建证书：

```bash
# 生成证书
openssl req -days 3650 -x509 -sha256 -nodes -newkey rsa:2048 -keyout nginx.key -out nginx.crt
```

<br>



## https优化



### 打开keepalive长连接

通过添加`keepalive`参数来打开长连接，例如：

```nginx
# 长连接保持100秒
server {
  listen 443 ssl;
  server_name localhost;
  
  keepalive_timeout 100;

  ssl_certificate /etc/nginx/ssl/nginx.crt;
  ssl_certificate_key /etc/nginx/ssl/nginx.key;

  ......
}
```





### 设置ssl session缓存

通过添加`ssl_session_cache`和`ssl_session_timeout`来打开ssl缓存：

```nginx
# 开启10M的缓存，大约可存储8K-10K个会话，10分钟后缓存过期。
server {
  listen 443 ssl;
  server_name localhost;
  
  ssl_session_cache shared:SSL:10m;
  ssl_session_timeout 10m;

  ssl_certificate /etc/nginx/ssl/nginx.crt;
  ssl_certificate_key /etc/nginx/ssl/nginx.key;

  ......
}
```

