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

