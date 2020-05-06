### 默认启动nginx

<br>



```bash
/usr/local/nginx/sbin/nginx
```



> 会读取默认路径下的配置文件，例如：/usr/local/nginx/conf/nginx.conf



<br>

### 指定配置文件和安装目录启动

<br>



指定配置文件启动：

```bash
/usr/local/nginx/sbin/nginx -c /etc/nginx.conf
```



指定安装目录启动：

```bash
/usr/local/nginx/sbin/nginx -p /usr/local/nginx
```



<br>



### 指定临时参数

<br>



可以砸启动的时候指定临时的**全局配置参数**，使新的配置生效：

```bash
# 将pid文件写到新的文件中
/usr/local/nginx/sbin/nginx -g "pid /var/nginx/test.pid"
```



<br>

### 检查配置是否错误

<br>



```bash
/usr/local/nginx/sbin/nginx -t
```



加上`-q`参数可以不把错误信息输出到屏幕：

```bash
/usr/local/nginx/sbin/nginx -t -q
```



<br>



### 检查版本信息和编译信息

<br>



```bash
# 输出版本信息
/usr/local/nginx/sbin/nginx -v

# 输出编译时指定的参数
/usr/local/nginx/sbin/nginx -V
```



<br>



### 服务状态控制

<br>



```bash
# 强制停止服务（立即退出进程）
/usr/local/nginx/sbin/nginx -s stop

# 优雅停止服务(先处理完当前请求)
/usr/local/nginx/sbin/nginx -s quit

# 重新读取配置
/usr/local/nginx/sbin/nginx -s reload
```



<br>



### 日志滚动

<br>



```bash
/usr/local/nginx/sbin/nginx -s reopen
```



上边的命令执行后，可以将原先的日志改名或者移动到其他目录进行备份，之后nginx会重新打开一个日志文件进行写入；这样可以防止日志文件过大。