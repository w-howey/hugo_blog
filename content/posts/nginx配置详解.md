---
draft: false
date: 2024-05-09T17:43:02+08:00
title: "nginx配置详解"
description: "nginx配置详解"
slug: "2024050917"
authors: ["howey"]
tags: ["nginx"]
categories: ["后端"]
externalLink: ""
series: ["编程系列"]
---

# Nginx配置详解及优化
### Nginx的配置文件通常分为几个部分：main（全局设置）、events（事件设置）、http（http相关设置）以及server（服务器特定设置）。每个部分都包含了影响Nginx行为的指令。

### nginx.conf

```

#定义了Nginx进程运行的用户和组，这里使用的是nginx用户和组
user  nginx nginx;
# 定义了Nginx的工作进程数，这里设置为2，通常与CPU核心数相匹配
worker_processes  2;
#将work process绑定到特定cpu上，避免进程在cpu间切换的开销, 
#如2核为01 10，四核为0001 0010 0100 1000，依次类推；有多少个核，就有几位数，最多开启8个
#1表示该内核开启，0表示该内核关闭。
worker_cpu_affinity 01 10;
# 错误日志存在目录及日志级别[ debug | info | notice | warn | error | crit ]
error_log  /var/log/nginx/error.log warn;
# 指定 pid 存放的路径
pid        /var/run/nginx.pid;
# 定义了工作进程的最大文件打开数限制，最好小于/etc/security/limits.conf 系统配置里的nofile 数量
worker_rlimit_nofile 32000;

events {
    #单个进程允许的客户端连接数，一般比worker_rlimit_nofile小
    worker_connections  16000;
    # 告诉nginx收到一个新连接通知后接受尽可能多的连接
    multi_accept on;
    #epoll比poll和select性能更好，适用于高并发场景
    use epoll;
}


http {
    #包含MIME类型配置文件
    include       /etc/nginx/mime.types;
    #定义默认的MIME类型
    default_type  application/octet-stream;
    #定义了访问日志的格式和存放路径，更多格式设置可参考官方文档
    log_format main  '$remote_addr - $remote_user [$time_local] "$request" $status $body_bytes_sent "$http_referer" "$http_user_agent" "$request_uri"';
    access_log  /var/log/nginx/access.log  main;

    # 启动内容压缩，有效降低网络流量
    gzip  on;
    # 过短的内容压缩效果不佳，压缩过程还会浪费系统资源
    gzip_min_length 10k;
    # 可选值1~9,压缩级别越高压缩率越高，但对系统性能要求越高
    gzip_comp_level 4;
    # 压缩的内容类别
    gzip_types text/plain text/css application/javascript application/x-javascript application/json text/xml application/xml application/xml+rss application/x-httpd-php text/javascript image/jpeg image/gif image/png;
    # 是否在http header中添加Vary: Accept-Encoding，建议开启
    gzip_vary on;
    gzip_static on;
    # 设置缓存路径并且使用一块最大100M的共享内存，用于硬盘上的文件索引，包括文件名和请求次数，每个文件在1天内若不活跃（无请求）则从硬盘上淘汰，硬盘缓存最大10G，满了则根据LRU算法自动清除缓存。
    proxy_cache_path /var/cache/nginx/cache levels=1:2 keys_zone=imgcache:100m inactive=1d max_size=10g;
    # 错误页面隐藏nginx版本，提高安全性
    server_tokens off;
    #启用错误页面的拦截
    proxy_intercept_errors on;
    fastcgi_intercept_errors on;
    #启用内核复制模式, 大幅提高IO效率
    sendfile       on;
    #必须在sendfile开启模式才有效，防止网路阻塞，积极的减少网络报文段的数量（将响应头和正文的开始部分一起发送，而不一个接一个的发送。）
    tcp_nopush     on;
    #HTTP长连接保持时长
    keepalive_timeout  30;
    #在一次长连接上所允许请求的资源的最大数，根据自己站点访问量进行调整
    keepalive_requests 500;
    #也是防止网络阻塞，不过要包涵在keepalived参数才有效
    tcp_nodelay on;
    #设置请求体的超时时间。我们也可以把这个设置低些，超过这个时间没有发送任何数据，
    client_body_timeout 15;
    #设置请求头的超时时间。我们也可以把这个设置低些，如果超过这个时间没有发送任何数据，nginx将返回request time out的错误。
    client_header_timeout 15;
    #告诉nginx关闭不响应的客户端连接。这将会释放那个客户端所占有的内存空间。
    reset_timedout_connection on;
    #响应客户端超时时间，这个超时时间仅限于两个活动之间的时间，如果超过这个时间，客户端没有任何活动，nginx关闭连接。
    send_timeout 15;
    #上传文件大小及缓冲区大小限制
    client_max_body_size 20M;
    client_body_buffer_size 1M;
    # 最大缓存数量，文件未使用存活期
    open_file_cache max=20000 inactive=20s;
    # 验证缓存有效期时间间隔
    open_file_cache_valid 30s;
    # 有效期内文件最少使用次数
    open_file_cache_min_uses 2;
    # 指定是否在搜索一个文件时记录cache错误
    open_file_cache_errors on;
    #限制IP在1s内最多访问30次，这里定义了一个共享内存区域的名字为 allips，
    #并且分配了 10m（10兆字节）的内存大小给这个区域，超出这个频率的请求将被延迟处理，以此来防止滥用或者分布式拒绝服务（DDos）攻击
    #具体设置多少根据自己网站流量进行修改
    limit_req_zone $binary_remote_addr zone=allips:10m rate=30r/s;

    # 如果客户端请求升级连接到 WebSocket，Nginx 将通过设置适当的请求头来代理这些请求到配置的后端服务器
    map $http_upgrade $connection_upgrade {
        default upgrade;
        '' close;
    }
    
    #具体站点配置目录
    include /etc/nginx/conf.d/*.conf;
}
```

### server部分详解

```
server {
    listen       80;  #监听的端口
    server_name admin.****.com; # 访问的域名
    
    # 直接返回301重定向到HTTPS，如果没有ssl证书，将下面server 除去SSL证书配置部分COPY到这里就可以了
    return 301 https://$server_name$request_uri;
}

server {
    listen       443 ssl; #监听的端口
    server_name admin.****.com;
    
    # SSL证书配置，具体配置要参考ssl颁发机构部署说明
    ssl_certificate /etc/nginx/cert/certificate.pem;
    ssl_certificate_key /etc/nginx/cert/certificate.key;
    ssl_session_timeout 5m;
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    ssl_prefer_server_ciphers on;

    #设置为 off 后端服务器可以收到原始的 URL
    proxy_redirect off;
    #后端服务器可以获得原始请求的 Host 头部信息
    proxy_set_header Host $host;
    # 后端服务器识别客户端的真实 IP 地址
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    #指示原始请求使用的是 HTTP 还是 HTTPS   
    proxy_set_header X-Forwarded-Proto $scheme;
    #表示原始请求使用的端口号
    proxy_set_header X-Forwarded-Port $server_port;
    #将客户端请求中的 Cookie 头部传递给后端服务器
    proxy_set_header Cookie $http_cookie;

    # 定义请求的处理规则，可以匹配不同的URL路径或正则表达
    location / {
        #指定网站内容的根目录
        root /usr/share/nginx/html;
        #定义默认首页文件，当请求一个目录时使用
        index index.html index.htm;
    }

    # 路径中如有api则转发到本机的8001服务端口
    location ^~ /api/ {
          rewrite ^/api/(.*)$ /$1 break;
          proxy_pass http://127.0.0.1:8001;
    }   
 
    # 静态文件
    location .\.(gif|jpg|jpeg|png|bmp|swf|ioc|rar|zip|txt|flv|mid|doc|ppt|pdf|xls|mp3|wma)$ {
        # 验证 Referer 头部，用于防盗链， 
        # none 表示不包括任何来源，
        # blocked 表示所有未明确列出的来源都将被拒绝
        # 正则表达式，表示所有以 .example.com 结尾的域名都是有效的来源。
        valid_referers none blocked server_names ~\.example\.com$;
        #定义了当请求被拒绝时返回的自定义错误页面
        error_page 403 /usr/share/nginx/403.html;
        #不在 Nginx 的访问日志文件中记录这些请求
        access_log  off;
        #缓存有效期为 30 天
        expires 30d;
    }
    
    # 当 HTTP 状态码为 500、502、503 或 504 的错误时转到50x.html页面
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }
}
```

