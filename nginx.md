# NGINX学习笔记

## 一、nginx概述

​		nginx是一个高性能的http和反向代理web服务器，同时也提供了imap、pop3、

SMTP服务，因为其丰富的功能集，稳定性，示例配置文件和低系统资源的消耗受到了开发者的欢迎

## 二、nginx的特点

1. 高并发，高性能
2. 模块化的架构使得它的扩展性非常好
3. 异步非阻塞的事件驱动模型
4. 长时间的稳定运行

## 三、IO模型



## nginx的常用配置

#### 		

#### 监听端口

```
server {
# Standard HTTP Protocol http协议
listen 80;
# Standard HTTPS Protocol https的协议
listen 443 ssl;
# For http2 http2协议
listen 443 ssl http2; 
# Listen on 80 using IPv6
listen [::]:80;
# Listen only on using IPv6
listen [::]:80 ipv6only=on;
}
```

### 监听域名

```
server {
# Listen to yourdomain.com
server_name yourdomain.com;
# Listen to multiple domains server_name yourdomain.com www.yourdomain.com;
# Listen to all domains
server_name *.yourdomain.com;
# Listen to all top-level domains
server_name yourdomain.*;
# Listen to unspecified Hostnames (Listens to IP address itself)
server_name "";
}
```

### 静态资源

```
server {
listen 80;
server_name yourdomain.com;
location / {
root /path/to/website;
}
}
```

### 重定向

```
server {
listen 80;
server_name www.yourdomain.com;
return 301 http://yourdomain.com$request_uri;
}
server {
listen 80;
server_name www.yourdomain.com;
location /redirect-url {
return 301 http://otherdomain.com;
}
}
```

### 反向代理

```
server {
listen 80;
server_name yourdomain.com;
location / {
proxy_pass http://0.0.0.0:3000;
# where 0.0.0.0:3000 is your application server (Ex: node.js) bound on 0.0.0.0 listening on port 3000
}
}
```

### 负载均衡

```
upstream node_js {
server 0.0.0.0:3000;
server 0.0.0.0:4000;
server 123.131.121.122;
}
server {
listen 80;
server_name yourdomain.com;
location / {
proxy_pass http://node_js;
}
}
```

### SSL协议

```
server {
listen 443 ssl;
server_name yourdomain.com;
ssl on;
ssl_certificate /path/to/cert.pem;
ssl_certificate_key /path/to/privatekey.pem;
ssl_stapling on;
ssl_stapling_verify on;
ssl_trusted_certificate /path/to/fullchain.pem;
ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
ssl_session_timeout 1h;
ssl_session_cache shared:SSL:50m;
add_header Strict-Transport-Security max-age=15768000;
}
# Permanent Redirect for HTTP to HTTPS
server 
{
listen 80;
server_name yourdomain.com;
return 301 https://$host$request_uri;
}
```

