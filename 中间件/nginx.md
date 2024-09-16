## 一、Nginx介绍

### 1. 什么是 Nginx

Nginx 是一个高性能的 HTTP 和反向代理服务器，同时也是一个 IMAP/POP3/SMTP 代理服务器。与传统服务器（如 Apache）相比，Nginx 以轻量、高效、响应快著称。





### 2. Nginx 的常见用途

- **静态资源服务**：直接为客户端提供静态文件，如 HTML、CSS、JS、图片等。
- **反向代理**：将客户端的请求转发到后端服务器，并将后端服务器的响应返回给客户端。
- **负载均衡**：在多台服务器之间分配请求，分散流量压力。
- **动静分离**：将静态文件和动态请求分开，提升网站性能。



## 二、Nginx 安装

### 1. 使用包管理工具安装（以 CentOS 为例）

```
sudo yum install nginx -y
```





### 2. 启动、停止、重启 Nginx

```
sudo systemctl start nginx    # 启动
sudo systemctl stop nginx     # 停止
sudo systemctl restart nginx  # 重启
```





### 3. 检查 Nginx 状态

```
sudo systemctl status nginx
```





### 4. 测试配置文件是否正确

```
nginx -t
```







## 三、Nginx 配置

Nginx 的主配置文件通常位于 `/etc/nginx/nginx.conf`。

### 1. 配置结构

Nginx 的配置文件主要由指令块构成，每个指令块控制不同的功能。主要配置结构如下：

```
user  nginx;
worker_processes  1;

events {
    worker_connections  1024;
}

http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

    sendfile        on;
    keepalive_timeout  65;

    server {
        listen       80;
        server_name  localhost;

        location / {
            root   /usr/share/nginx/html;
            index  index.html index.htm;
        }

        error_page  500 502 503 504 /50x.html;
        location = /50x.html {
            root   /usr/share/nginx/html;
        }
    }
}
```





### 2. 核心配置参数

- **worker_processes**：表示工作进程数，通常设置为 CPU 核数，提升并发处理能力。
- **worker_connections**：每个进程的最大连接数。
- **keepalive_timeout**：保持客户端连接的超时时间。
- **sendfile**：开启文件传输优化，提升静态文件传输效率。





### 3. 反向代理配置

```
server {
    listen 80;
    server_name example.com;

    location / {
        proxy_pass http://backend_server;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

- **proxy_pass**：指定后端服务器地址，Nginx 会将请求转发给这个地址。
- **proxy_set_header**：为转发的请求设置头信息，常用来保留客户端的真实 IP。





### 4. 负载均衡配置

```
server {
    location / {
        proxy_pass http://backend;
    }
}
```

- **upstream**：定义后端服务器集群，可以为服务器设置权重（`weight`），实现流量分配。
- **proxy_pass**：将请求转发给 `upstream` 定义的服务器集群。





### 5. 动静分离配置

```
server {
    listen 80;
    server_name example.com;

    location /static/ {
        root /var/www/static/;
    }

    location / {
        proxy_pass http://backend_server;
    }
}
```

- 静态文件通过 `location /static/` 直接从本地目录提供。
- 动态请求通过 `proxy_pass` 转发给后端处理。







------

## 四、Nginx 性能优化

### 1. 调整 `worker_processes` 和 `worker_connections`

根据服务器的硬件资源，调整 `worker_processes` 为 CPU 核心数，`worker_connections` 设置为合理值（如 1024）。

```
worker_processes auto;
worker_connections 1024;
```





### 2. 使用 `sendfile` 和 `tcp_nopush`

开启文件传输优化，提升静态资源的传输效率。

```
sendfile on;
tcp_nopush on;
```





### 3. 缓存配置

可以通过设置 `expires` 指令为静态资源添加缓存，减少服务器负载。

```
location /static/ {
    expires 30d;
}
```





### 4. 开启 Gzip 压缩

Gzip 压缩可以减少传输的数据量，提升页面加载速度。

```
gzip on;
gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;
```

------







## 五、Nginx 常见错误与排查

### 1. 检查配置文件语法错误

```
nginx -t
```



### 2. 查看日志

- **错误日志**：`/var/log/nginx/error.log`
- **访问日志**：`/var/log/nginx/access.log`



### 3. 端口占用问题

当 Nginx 无法启动时，可能是 80 端口被占用。可以使用以下命令查找占用端口的进程：

```
sudo netstat -tuln | grep 80
```





### 4. 提高 Nginx 最大文件描述符限制

如果遇到 "too many open files" 错误，可以在 `/etc/nginx/nginx.conf` 中增加以下配置：

```
worker_rlimit_nofile 65535;
```

------




