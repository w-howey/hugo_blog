---
title: "docker-compose搭建minio服务"
date: 2024-06-05T15:21:16+08:00
draft: false
slug: "24060512212"
authors: ["howey"]
tags: ["minio"]
series: ["编程系列"]
categories: ["后端"]
---
### 配置文件：docker-compose.yml
```
version: '3'
services:
    minio:
        image: minio/minio:latest
        container_name: my-minio
        environment:
            MINIO_ROOT_USER: howey
            MINIO_ROOT_PASSWORD: 12345678 #密码必须八位
        ports:
            - "9000:9000"
            - "9001:9001" # 可选，用于HTTPS
        volumes:
            - ./data:/data
        command: server /data --console-address ":9001"
networks: # 配置docker network
    app_net:
        driver: bridge
```