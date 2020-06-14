需要重新编译lua环境，默认nginx是不支持lua扩展模块的。

<br>

## 下载LuaJIT

luajit是更高效的lua解释器。

```bash
wget http://luajit.org/download/LuaJIT-2.0.2.tar.gz
tar zxf LuaJIT-2.0.2.tar.gz
make install PREFIX=/usr/local/LuaJIT
export LUAJIT_LIB=/usr/local/LuaJIT/lib
export LUAJIT_INC=/usr/local/LuaJIT/include/luajit-2.0
```



<br>



## 下载相关文件

需要下载`nginx_devel_kit`和`lua-nginx-module`这两个nginx开发库和模块文件

```bash
cd /opt
wget https://github.com/simpl/ngx_devel_kit/archive/v0.3.0.tar.gz
wget https://github.com/openresty/lua-nginx-module/archive/v0.10.9rc7.tar.gz
tar zxf v0.3.0.tar.gz
tar zxf v0.10.9rc7.tar.gz
```

<br>



## 重新编译nginx

```bash
wget http://nginx.org/download/nginx-1.8.0.tar.gz

./configure --prefix=/etc/nginx --sbin-path=/usr/sbin/nginx --modules-path=/usr/lib64/nginx/modules --conf-path=/etc/nginx/nginx.conf --error-log-path=/var/log/nginx/error.log --http-log-path=/var/log/nginx/access.log --pid-path=/var/run/nginx.pid --lock-path=/var/run/nginx.lock --http-client-body-temp-path=/var/cache/nginx/client_temp --http-proxy-temp-path=/var/cache/nginx/proxy_temp --http-fastcgi-temp-path=/var/cache/nginx/fastcgi_temp --http-uwsgi-temp-path=/var/cache/nginx/uwsgi_temp --http-scgi-temp-path=/var/cache/nginx/scgi_temp --user=nginx --group=nginx --with-compat --with-file-aio --with-threads --with-http_addition_module --with-http_auth_request_module --with-http_dav_module --with-http_flv_module --with-http_gunzip_module --with-http_gzip_static_module --with-http_mp4_module --with-http_random_index_module --with-http_realip_module --with-http_secure_link_module --with-http_slice_module --with-http_ssl_module --with-http_stub_status_module --with-http_sub_module --with-http_v2_module --with-mail --with-mail_ssl_module --with-stream --with-stream_realip_module --with-stream_ssl_module --with-stream_ssl_preread_module --with-cc-opt=’-O2 -g -pipe -Wall -Wp,-D_FORTIFY_SOURCE=2 -fexceptions -fstack-protector-strong --param=ssp-buffer-size=4 -grecord-gcc-switches -m64 -mtune=generic -fPIC’ --with-ld-opt=’-Wl,-z,relro -Wl,-z,now -pie’ --add-module=/opt/ngx_devel_kit-0.3.0 --add-module=/opt/lua-nginx-module-0.10.9rc7

make -j 4 && make install

# 加载lua库，加入到ld.so.conf文件
echo “/usr/local/LuaJIT/lib” >> /etc/ld.so.conf
ldconfig
```

