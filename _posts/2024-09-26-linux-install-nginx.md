---
title: Linux系统离线安装Nginx
published: true
---


# 通过源码安装nginx

## 1、安装包

先到官网下载 openssl，zlib，pcre，nginx 源码安装包，如果系统重没有gcc 也要下载

安装顺序为openssl，zlib，pcre，nginx，都是先解压—>然后定位到指定的目录，执行编译安装——>./configure——>make——>make install。

其中 openssl 安装时  Configure 首字母大写。

安装nginx时需要安装ssl模块,如下

```

./configure --with-http_ssl_module

make

make install

```


注：如果 OpenSSL 已安装，但 Nginx 仍然找不到库，可以更新共享库路径：

- 更新 `/etc/ld.so.conf.d/`： 创建一个新配置文件（例如 `openssl.conf`）并添加 OpenSSL 库的路径

先通过命令查找libssl.so所在目录
find /usr/local/ -name "libssl.so*"
如果在 /usr/local/lib64 下，配置如下

```
echo "/usr/local/lib64" | sudo tee /etc/ld.so.conf.d/openssl.conf
```

 - 更新库缓存： 运行以下命令以更新库缓存
```
sudo ldconfig
```


## 2、启动Nginx

nginx 默认安装目录 /usr/local/nginx 。

启动nginx ，指定配置文件

```
/usr/local/nginx/sbin/nginx 
```

查看版本
```
/usr/local/nginx/sbin/nginx -V
```

测试访问：
```
curl http://127.0.0.1
```


## 3、配置https服务

### 3.1 配置ssh证书

```
openssl genrsa -out /etc/pki/nginx/private/server.key 2048

openssl req -new -key /etc/pki/nginx/private/server.key -out server.csr

openssl x509 -req -days 365 -in server.csr -signkey /etc/pki/nginx/private/server.key -out /etc/pki/nginx/server.crt

```
### 3.2 配置443端口监听

vi /usr/local/nginx/conf/nginx.conf

在最后面添加如下部分

```
server {
    listen       443 ssl  default_server;                              
	listen       [::]:443 ssl  default_server;                      
	server_name  _;                                                          
	ssl_certificate "/etc/nginx/server.crt";                                         ssl_certificate_key "/etc/nginx/server.key";                                     ssl_session_cache shared:SSL:1m;                                                 ssl_session_timeout  10m;                                                        ssl_prefer_server_ciphers on;
    location / {
        proxy_pass https://x.x.x.x;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
```

### 3.3 更新配置

启动服务，指定配置文件

```
/usr/local/nginx/sbin/nginx -s reload -c /usr/local/nginx/conf/nginx.conf
```

测试访问：
```
curl -k https://127.0.0.1
```


# 通过docker容器安装nginx

前提要在目标机器安装docker服务，可通过 docker -v 命令查看docker版本。

## 1、制作nginx容器包

### 1.1、下载最新容器包

```
docker pull --platform=linux/amd64  nginx:latest

```

### 1.2、导出离线包
```
docker save -o nginx.tar nginx:latest

```

### 1.3、拷贝到目标机器并加载容器

```
docker load -i nginx.tar 
```

## 2、nginx https 配置

### 2.1 生成安全证书

```
openssl genrsa -out /etc/pki/nginx/private/server.key 2048

openssl req -new -key /etc/pki/nginx/private/server.key -out server.csr

openssl x509 -req -days 365 -in server.csr -signkey /etc/pki/nginx/private/server.key -out /etc/pki/nginx/server.crt

```

### 2.2 配置https服务文件

mkdir /data/nginx/conf && vi /data/nginx/conf/nginx.conf

```
server {
    listen       443 ssl  default_server;                              
	listen       [::]:443 ssl  default_server;                      
	server_name  _;                                                          
	ssl_certificate "/etc/nginx/server.crt";                                         ssl_certificate_key "/etc/nginx/server.key";                                     ssl_session_cache shared:SSL:1m;                                                 ssl_session_timeout  10m;                                                        ssl_prefer_server_ciphers on;
    location / {
        proxy_pass https://x.x.x.x;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}

```

## 3、启动docker 容器

### 3.1 启动docker 
```
docker run --name nginx -v /data/nginx/conf/nginx.conf:/etc/nginx/nginx.conf  -v /etc/pki/nginx/private/server.key:/etc/nginx/server.key  -v /etc/pki/nginx/server.crt:/etc/nginx/server.crt -p 443:443  -d nginx

```

### 3.2 测试服务

```
curl -k https://127.0.0.1
```
