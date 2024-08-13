---
title: "docker 数据卷挂"
date: 2024-08-13T22:21:16+08:00
draft: false
slug: "docker-240813"
authors: ["howey"]
tags: ["docker"]
series: ["编程系列"]
categories: ["运维"]
---
## 数据卷挂载
### 将`home/wwwroot/default`数据卷挂载到容器`/usr/share/nginx/html`中

`docker run -d -p 80:80 -v home/wwwroot/default:/usr/share/nginx/html --name nginx nginx`

### 查看挂载卷信息
`docker volume inspect /home/wwwroot/default`

### 查看镜像的挂载卷信息
`docker inspect nginx`

### mysql 会自动将数据存储挂载到宿主机 是匿名卷

宿主机与容器的卷挂载
本地目录必须以`/`开头或者"./"开头 否则就是数据卷挂载
```
-v mysql:/var/lib/mysql # 数据卷
-v ./mysql:/var/lib/mysql # 本地卷
```
所以mysql一般挂载如下：
```
-v /root/mysql/data:/var/lib/mysql
-v /root/mysql/init:/docker-entrypoint-initdb.d # 初始化脚本
-v /root/mysql/conf:/etc/mysql/conf.d 
```
