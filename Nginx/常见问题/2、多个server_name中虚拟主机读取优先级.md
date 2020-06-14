## 相同server_name多个虚拟主机优先级访问

例如，配置了两个具有相同server_name的虚拟主机：

```nginx
# server1.conf
server {
  listen 80;
  server_name testserver1 server.example.com;
  location {
    ......
  }
}

# server2.conf
server {
  listen 80;
  server_name testserver2 server.example.com;
  location {
    ......
  }
}
```



对于这种情况，nginx会将请求转发给最先读取的server配置，对于上边两个配置来说，就会先发给server1.conf；