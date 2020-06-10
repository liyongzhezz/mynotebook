## 什么是rewrite

rewrite是nginx基于正则表达式，对请求路径重写，主要用于：

- URL访问跳转，支持开发设计；
- 兼容性支持；
- 精简展示URL；
- SEO优化；
- 流量转发；
- 伪装页面，安全；
- 网站维护时重定向到维护页面；

<br>



## 配置语法

```nginx
rewrite <regex> <replacement> [flag];
# 可以配置在server、location下
```



flag表示rewrte对应的类型：

- last：停止rewrite检测；
- break：停止rewrite检测；
- redirect：返回302临时重定向，地址栏会显示跳转后的地址；
- permanent：返回301永久重定向，地址栏会显示跳转后的地址； 



例如：

```nginx
# 将所有请求重定向到 /pages/index.html
rewrite ^(.*)$ /pages/index.html break;
```



<br>

 

## 实例

```nginx
server {
  listen 80;
  server_name rewrite.example.com;
  
  access_log /var/log/nginx/log/rewrite_access.log main;
  
  root /app/code;
  
  location ~ ^/break {
    rewrite ^/break /test/ break;
  }
  
  location ~ ^/last {
    rewrite ^/last /test/ last;
  }
  
  location /test/ {
    default_type application/json;
    return 200 '{"status": "success"}‘;
  }
}
```



当访问`/break`和`/last`时，都会定向到`/test`，但是rewrite的类型不同。

- 当访问`/break`时，返回404；
- 当访问`/last`是，正常跳转到`/test`；



break和last模式，都会先匹配对应的location，break匹配到后，不会新建请求，而是在当前的location下，在指定的root目录中根据rewrite查找文件，因为这里/app/code下不存在test文件，所以返回404；last模式会将请求根据重定向规则改写并重新创建一个请求，可以跳出当前location去匹配其他location，所以它匹配到了第三个location，也就获得了预期的返回值。



last和redirect的区别在于，使用last的时候客户端向服务端发起的就是一次请求；而使用redirect时，会先返回302以及新的url地址，然后客户端再请求新的url地址，总共2次请求。



<br>



## 优先级

在server块下的rewrite优先级最高，会对所有请求进行重写，一般用于网站整体维护；



在location下的rewrite仅对匹配到的location生效；