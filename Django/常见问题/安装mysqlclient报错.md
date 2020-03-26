在mac上的django项目中安装mysqlclient时出现如下的报错：

`ld: library not found for -lssl`



这个问题可能是两个原因导致的：

1、没有安装openssl，可以使用如下的方式安装：

```shell
brew install openssl
pip install mysqlclient
```



2、pip寻找依赖机制问题，需要指定openssl库：

```shell
LDFLAGS=-L/usr/local/opt/openssl/lib pip install mysqlclient
```

