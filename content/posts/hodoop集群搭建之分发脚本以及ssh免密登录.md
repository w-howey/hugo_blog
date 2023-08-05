---
title: "hodoop集群搭建之分发脚本以及ssh免密登录"
date: 2023-07-26T22:21:16+08:00
draft: false
tags: ["hadoop", "ssh", "shell"]
series: ["编程系列"]
categories: ["大数据"]
---

# hodoop 介绍

---

@[TOC](文章目录)

---

# 一、hadoop 三种运行模式

> 本地模式 ：数据存储在 linux 本地（只是在测试偶尔用）
> 伪分布式：数据存储在 HDFS（一台主机模拟多主机）
> 完全分布式：数据存储在 HDFS（多台服务器工作）企业大量使用

# 二、模式示例

## 1.本地模式

> 在 hadoop 文件夹下新建

```linux
mkdir wcinput
cd wcinput
vim word.txt  //任意写入内容
```

./wcoutput 要是不存在的文件夹，不然会抛出异常

```linux
hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-3.3.1.jar wordcount wcinput/ ./wcoutput
cat part-r-00000
```

## 2.完全分布式模式

### （1）流程

1.  准备 3 台客户机（关闭防火墙，静态 ip，主机名称）
2.  List item
3.  安装 jdk，hadoop
4.  配置环境变量
5.  配置集群
6.  单点启动
7.  配置 ssh
8.  群起测试集群

### （2）编写集群分发脚本 xsync

#### 1.scp 安全拷贝

1.  scp 定义：
    scp 可以实现服务器与服务器之间的数据拷贝
2.  基本语法：
    scp -r 要拷贝的文件路径/名称 目的地用户名@主机:目的地路径名称
    -r 表示递归
    例子：102 推送至 103 `scp -r jdk1.8.0_331/ root@hadoop103:/opt/module/`
    103 从 102 拉取：`scp -r root@hadoop102:/opt/module/hadoop-3.3.1 ./`
    103 拷贝 102 到 104：
    `scp -r root@hadoop102:/opt/module/* root@hadoop104:/opt/module/`

#### 2.rsync 远程同步工具

rsync 主要用于备份和镜像。具有速度快、避免复制相同内容和支持符号链接的优点
rsync 和 scp 的区别：用 rsync 做文件的复制要比 scp 的速度快，rsync 只对差异文件做更新。scp 是把所有文件都复制过去
（1）基本语法
rsync -av 要拷贝的文件路径/名称 目的地用户名@主机:目的地路径名称
-a 表示归档拷贝
-v 显示复制过程
示例：`rsync -av hadoop-3.3.1/  root@hadoop103:/opt/module/`

#### 3.集群分发脚本 xsync

```shell
#!/bin/bash

#1.判断参数个数

if [ $# -lt 1 ]
	then
		echo not enough argument!
	exit;
fi

#2.便利集群所有机器
for host in hadoop102 hadoop103 hadoop104
do
	echo ===========================================$host==========================
	#3.遍历所有目录
	for file in $@
	do
		#4.判断文件是否存在
		if [ -e $file ]
			then
				#5.获取父目录(软连接的处理)
				pdir=$(cd -P $(dirname $file); pwd)

				#6.获取当前文件的名称
				fname=$(basename $file)
				ssh $host "mkdir -p $pdir"
				rsync -av $pdir/$fname $host:$pdir
			else
				echo $file does not exists!
		fi
	done
done
```

给权限执行
`chmod 777 xsync `
拷贝全局可用
`sudo cp /home/howey/bin/xsync /bin/`
执行示例
`xsync a.txt`

#### 4.ssh 免密登录（切换用户需要再次执行）

生成密钥
`ssh-keygen -t rsa`
将本地的 ssh 公钥文件安装到远程主机对应的账户下
`ssh-copy-id hadoop103`
免密原理：
**A 服务器：生成密钥对，将 A 公钥安装到 B 服务器下，
当 Assh 访问 B 时携带经过私钥 A 加密的数据，B 服务器接收到之后，去授权 key 查找到 A 的公钥，根据服务器 A 的公钥进行解密。
返回的数据采用 A 的公钥进行加密返回给 A，A 接收返回的数据采用私钥 A 进行解密。**
