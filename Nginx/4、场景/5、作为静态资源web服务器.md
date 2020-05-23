<br>

nginx作为静态资源服务器时，可以接受用户对jpeg、html、flv等格式的静态资源的请求，并通过静态资源存储将文件返回给客户端。常用在动静分离的场景下。

<br>

## 静态资源类型

静态资源是不需要服务器动态运行生成的资源：

- 浏览器渲染类：html、css、js；
- 图片：jpeg、gif、png；
- 视频：flv、mpeg；
- 文件：txt等任意下载文件；

<br>



## 静态资源服务场景--cdn

cdn是动态内容分发网络，他可以根据用户所在地区，将用户请求调度到最近的静态服务器，这样可以加快响应，提高用户体验。



例如，有一个核心的静态资源存储中心，会将静态资源同步到北京、上海、新疆等地的静态资源服务器；当新疆的用户请求时，会调度到新疆的静态资源服务器，北京的请求则调度到北京；



<br>



## 静态资源服务器常用模块

`send_file`模块是nginx作为静态资源服务器时常用到的提高文件读取效率的模块，配置语法如下：

```bash
sendfile on | off;

# 默认为off
# 可配置在http、server、location下
```



`tcp_nopush`数据包不会立刻发送给用户，而是将数据包进行整合，一次性发送给客户端；在`sendfile`开启的情况下使用，可以提高网络包传输效率，配置语法如下：

```bash
tcp_nopush on | off;

# 默认为off
# 可配置在http、server、location下
```



`tcp_nodelay`则是将数据包实时发送给用户而不等待，常用在实时性要求高的场景下，在配置keepalive时，提高数据包传输的实时性。配置语法如下：

```bash
tcp_nodelay on | off;

# 默认为on
# 可配置在http、server、location下
```



`gzip`可以压缩数据包的大小，减小带宽消耗，减小等待。配置语法如下：

```bash
gzip on | off;	# 是否开启压缩
gzip_comp_level <level>;		# 压缩比率
gzip_http_version 1.0 | 1.1;	# gzip指定http版本（主流是1.1）

# gzip默认为off，gzip_comp_level默认为1，gzip_http_version默认为1.1
# 可配置在http、server、location下
```



> 压缩比率越大，数据包越小，但是服务器性能消耗越高，需要根据实际调整；
>
> gzip对于文本的压缩效果比价好，对图片一般



<br>



## 其他模块

- `http_gzip_static_module`：预读gzip模块，会现在磁盘找.gz格式的压缩文件；
- `http_gunzip_module`：支持gunzip的压缩方式；为了解决少部分浏览器不支持gzip压缩的问题，很少用；



使用`http_gzip_static_module`的话，需要先把文件使用下面的命令进行压缩，成为`.gz`格式的文件：

```bash
gzip <文件名>
```



<br>



## 配置实例

```nginx
server {
  listen 80;
  server_name localhost;
  
  sendfile on;
  access_log /var/log/nginx/log/static_access.log main;
  
  location ~ .*\.(jpg|gif|png)$ {
  	gzip on;
    gzip_http_version 1.1;
    gzip_comp_level 2;
    gzip_types text/plain appication/javascript application/x-javascript text/css application/xml text/javascript application/x-http-php image/jpeg image/png image/gif;
    root /opt/app/code/images;
  }
  
  location ~.*\.(txt|xml)$ {
    gzip on;
    gzip_http_version 1.1;
    gzip_comp_level 1;
    gzip_types text/plain appication/javascript application/x-javascript text/css application/xml text/javascript application/x-http-php image/jpeg image/png image/gif;
    root /opt/app/code/doc;
  }
  
  location ~^/download {
    gzip_static on;
    tcp_nopush on;
    root /opt/app/code;
  }
  
  location = /50x.html {
    root /usr/share/nginx/html;
  }
}
  
```

<br>



- 访问`jpg|gif|png`结尾的文件会到`/opt/app/code/images`下查找；
- 访问`txt|xml`结尾的文件会到`/opt/app/code/doc`下查找；
- 访问`download`路径会到`/opt/app/code/download`下查找；