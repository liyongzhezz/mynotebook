<br>

所有访问地址都是：http://www.example.com/test/index.html 为例

<br>

## 有 /

```nginx
location  /test/ {
    proxy_pass  http://127.0.0.1:8080/;
}
```

测试结果，请求被代理跳转到：http://127.0.0.1:8080/index.html



<br>



## 无 /

```nginx
location  /test/ {        
    proxy_pass http://127.0.0.1:8080;
}
```

测试结果，请求被代理跳转到：http://127.0.0.1:8080/test/index.html

<br>



## 增加目录加 /

```nginx
location  /test/ {        
    proxy_pass http://127.0.0.1:8080/sun/;
}
```

测试结果，请求被代理跳转到：http://127.0.0.1:8080/sun/index.html

<br>



## 增加目录不加 /

```nginx
location  /test/ {
    proxy_pass http://127.0.0.1:8080/sun;
}
```

测试结果，请求被代理跳转到：http://127.0.0.1:8080/sunindex.html

<br>



