---
title: "docker 镜像结构"
date: 2024-08-13T23:21:16+08:00
draft: false
slug: "docker-24081323"
authors: ["howey"]
tags: ["docker"]
series: ["编程系列"]
categories: ["运维"]
---
## 分层结构
每一层一个layer
例如：一个java应用
包含：
1. 系统库 （基础镜像）
2. jre
3. jar 包
4. 启动脚本 （入口）

## dockerfile 
dockerfile 是一个docker镜像的构建文件，包含一系列指令
|指令|说明|示例|
|--|--|--|
|form|指定基础镜像||
|env|设置环境变量||
|copy|考本本地文件到镜像指定目录||
|run|执行linuxshell命令||
|expose|暴露的端口|expose 8080|
|entrypoint|应用的启动命令|entrypoint java -jar app.jar|

## 构建镜像
```
docker build -t <image_name>:1.0 .
# -t 给镜像取名·
# . 表示当前目录
```
// 保存镜像
```
docker save -o 
```
// 加载镜像
```
docker load -i
```
// 查看日志
```
docker log -f <image-name>
```

