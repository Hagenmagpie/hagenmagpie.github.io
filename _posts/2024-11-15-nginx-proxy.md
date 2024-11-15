---
title: nginx代理https支持跨域
---

nginx代理https地址，并支持跨域，可以解决下面2个问题

> has been blocked by CORS policy: Request header field aptp-token is not allowed by Access-Control-Allow-Headers in preflight response.

> has been blocked by CORS policy: The 'Access-Control-Allow-Origin' header contains multiple values '*, *', but only one is allowed.


示例如下：

```
server {
    # 启用 SSL 监听在端口 12345 上
    listen 12345 ssl;

    # 配置 server_name 指定服务器的主机名或 IP，使用 `_` 表示这个服务器块接受所有的请求，类似于默认服务器
    server_name _;

    # 配置 SSL 证书文件的路径，指定一个自签名证书
    ssl_certificate /etc/nginx/ssl/nginx-selfsigned.crt;

    # 配置 SSL 证书的私钥文件的路径
    ssl_certificate_key /etc/nginx/ssl/nginx-selfsigned.key;

    # 配置 Diffie-Hellman 参数文件，用于增强 SSL 安全性
    ssl_dhparam /etc/nginx/ssl/dhparam.pem;

    # 启用 TLS 协议，支持 TLSv1, TLSv1.1 和 TLSv1.2，但不包括 TLSv1.3（可能是为了兼容旧客户端）
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;

    # 启用服务器优先的密码套件选择，而不是客户端选择的密码套件
    ssl_prefer_server_ciphers on;

    # 配置 SSL 加密套件，定义一组支持的加密算法
    ssl_ciphers "EECDH+AESGCM:EDH+AESGCM:AES256+EECDH:AES256+EDH";

    # 设置 Elliptic Curve Diffie-Hellman (ECDH) 密钥交换算法使用的椭圆曲线，这里使用 secp384r1 曲线
    ssl_ecdh_curve secp384r1;

    # 配置 SSL 会话缓存，缓存大小为 10MB，用于提高 SSL 握手的性能
    ssl_session_cache shared:SSL:10m;

    # 禁用 SSL 会话票证（Session Tickets），以防止会话重用，增加安全性
    ssl_session_tickets off;

    # 定义代理服务，所有访问 / 的请求将被转发到指定的后端地址
    location / {
        # 将所有请求转发到 https://x.x.x.x:443/，后端服务器的地址是 x.x.x.x
        proxy_pass https://x.x.x.x:443/;

        # 隐藏源服务器的 'Access-Control-Allow-Origin' 响应头，防止其被暴露到客户端
        proxy_hide_header 'Access-Control-Allow-Origin'; 

        # 为响应添加 CORS 头部，允许所有域进行跨域访问
        add_header 'Access-Control-Allow-Origin' '*' always;

        # 将客户端请求中的 `Access-Control-Request-Headers` 头部值添加到响应中
        # 该头部列出客户端发起跨域请求时携带的自定义请求头
        add_header Access-Control-Allow-Headers $http_access_control_request_headers;

        # 设置 `Access-Control-Allow-Methods` 头部，允许所有常见的 HTTP 请求方法
        add_header 'Access-Control-Allow-Methods' '*';

        # 如果请求方法是 OPTIONS（预检请求），则直接返回 HTTP 200 响应，
        # 这表明允许跨域请求并且不再继续后续请求的处理
        if ($request_method = OPTIONS){
            return 200;
        }
    }
}

```

