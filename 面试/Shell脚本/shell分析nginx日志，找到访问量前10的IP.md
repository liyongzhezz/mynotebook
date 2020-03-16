nginx日志格式如下：

```nginx
202.101.129.218- - [26/Mar/2006:23:59:55 +0800] "GET /online/stat_inst.php?pid=d065HTTP/1.1" 302 20-"-" "-" "Mozilla/4.0(compatible; MSIE 6.0; Windows NT 5.1)"
```



```bash
awk '{print $1}' access.log |sort|uniq -c |head -n 10
```



输出为：

```reStructuredText
31 202.101.129.218
21 123.93.29.11
11 13.92.19.31
```

