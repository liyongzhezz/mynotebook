这里以安装python3.7.3版本为例。



```bash
# 安装依赖
yum install -y zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel readline-devel tk-devel gcc  libffi-devel

# 下载python
wget https://www.python.org/ftp/python/3.7.3/Python-3.7.3.tgz 

tar zxf Python-3.7.3.tgz
cd Python-3.7.3

# 编译安装
./configure --prefix=/usr/local/python3.7
make && make install

# 备份原来的python
mv /usr/bin/python /usr/bin/python.bak

# 创建软连接
ln -s /usr/local/python3.7/bin/python3.7 /usr/bin/python
ln -s /usr/local/python3.7/bin/pip3 /usr/local/bin/

python -V
Python 3.7.3
```



修改yum文件，编辑`/usr/bin/yum`和`/usr/libexec/urlgrabber-ext-down`，将文件头的`#!/usr/bin/python`改为`#!/usr/bin/python2`即可。

