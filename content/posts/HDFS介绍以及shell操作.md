---
title: "HDFS介绍以及shell操作"
date: 2023-07-26T22:21:16+08:00
draft: false
tags: ["hadoop", "HDFS", "shell"]
series: ["编程系列"]
categories: ["大数据"]
---

# HDFS

@[TOC]

## 一、HDFS 定义

HDFS 即（hadoop distributed file system），采用目录树来定位文件；其次，它是分布式的，由很多服务器联合起来实现其功能，集群中的服务器有各自的角色
HDFS 的使用场景：适合一次写入，多次读出的场景

## 二、HDFS 优缺点：

### 1.优点：

1.  高容错性 数据自动保存多个副本。通过增加副本的形式，提高容错性 某一个副本丢失以后，可以自动回复
2.  适合处理大数据 数据规模大的（甚至 PB），文件规模大的（上百万）
3.  可以构建在廉价机器上，通过多副本机制，提高可靠性

### 2.缺点：

1.  不适合低延时数据访问，比如毫秒级存储
2.  无法高效的对大量小文件进行存储（1.存储大量小文件，会占用 nameNode 大量的内存用来存储文件目录和块消息，namenode 内存总是有限的 2.小文件存储的寻址时间会超过读取时间，违反了 hdfs 的设计目的）
3.  不支持并发写入，文件的随机修改，（1.一个文件只能有一个写，不允许多个线程同时写 2.仅支持数据的追加，不支持修改）

## 三、HDFS 组织架构

![在这里插入图片描述](https://img-blog.csdnimg.cn/7b0d2fd1aaf9460283577efa4c5cd1e1.png)

client: 分别访问 NameNode 和 DataNode 以获取文件的元信息及内容。

SecondaryNamenode：辅助 namenode 用于定期合并 fsimage 和 edits，生成新的 fsimage，并推送给 Namenode，在紧急情况下 可用于恢复 namenode 部分数据

磁盘的传输速率决定设置块状大小
**寻址时间为传输时间的 1%时，则为最佳状态**
![在这里插入图片描述](https://img-blog.csdnimg.cn/a38e24b3c5794789a7393ed57d34deef.png)
块设置太小，增加寻址时间
块设置太大，会增加磁盘传输数据

## 四、HDFS 的 shell 操作

1.  hadoop fs 等同于 hdfs dfs
2.  hadoop fs -help rm（查看 rm 用法）
3.  `hadoop fs -moveFromLocal  本地文件 hdfs文件目录 ` （将本地文件剪切到 hdfs 目录下）
4.  `hadoop fs -copyFromLocal  本地文件 hdfs文件目录 ` （将本地文件复制到 hdfs 目录下等同于-put）
5.  `hadoop fs -appendToFile 本地文件 hdfs文件 ` (将本地文件追加到 hdfs 文件末尾)
6.  `hadoop fs -copyToLocal  hdfs文件 本地文件夹` (将 hdfs 文件拷贝到本地文件夹等同于-get)
7.  -ls,-cat,-chgrp,-chmod.-chown,-mkdir,-cp,-mv,-tail,-rm,-rm -r.-du 都和 linux 命令一样
8.  `hadoop fs -setrep -w 2 /test` 修改目录副本数，只是记录在 namenode 的元数据，是否真的有这么多取决于机器的台数
