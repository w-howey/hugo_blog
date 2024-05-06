---
title: "虚拟机安装centos"
date: 2021-07-26T22:21:16+08:00
draft: false
slug: "2107262213"
tags: ["centos", "虚拟机"]
series: ["编程系列"]
authors: ["howey"]
categories: ["运维"]
---

# 虚拟机安装 centos

---

@[TOC](文章目录)

---

# 一、安装前提

1.准备 Vm virtualbox 软件，[下载地址](https://www.virtualbox.org/)
下载完成之后，选择自己的目录，一路 next 完成安装 2.准备 linux centos 镜像，[下载地址](http://mirrors.aliyun.com/centos/7/isos/x86_64/)

---

# 二、安装 centos

## 1.运行 virtualbox，如下：

![在这里插入图片描述](https://img-blog.csdnimg.cn/d8e412b0550a48cebc9272b024e3457b.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAUG15eF93eWg=,size_20,color_FFFFFF,t_70,g_se,x_16)

## 2.点击新建，如下：

![在这里插入图片描述](https://img-blog.csdnimg.cn/07f2f730c18a443e866fb913b9a8cf3a.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAUG15eF93eWg=,size_20,color_FFFFFF,t_70,g_se,x_16)
名称随意填，文件夹创建新的 类型选择 linux 的

## 2.划分内存以及设置

![在这里插入图片描述](https://img-blog.csdnimg.cn/88fbb00c92ec44fbb0339b19ab5e4675.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAUG15eF93eWg=,size_16,color_FFFFFF,t_70,g_se,x_16)
**注意安装在哪个位置是随意的但需要保证有 20G 以上的大小，直接下一步，内存 2048M，虚拟硬盘 20G 然后下一步下一步 ok。
点击设置选择网络，网卡选择桥接网卡，这时点击 ok，你会发现有电脑不能点，报了一个错误，没有禁用虚拟化硬盘。这是因为你的你 Virtualization Technology 没有开启，需要进入 bios 开启一下**
![在这里插入图片描述](https://img-blog.csdnimg.cn/c3d37fb5154e4a46af1125d1013d382b.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAUG15eF93eWg=,size_20,color_FFFFFF,t_70,g_se,x_16)

## 3.导入盘片，点击存储中的盘片位置，选择自己下载的镜像文件

![在这里插入图片描述](https://img-blog.csdnimg.cn/c8040783870a4faea1abdd2036ebf407.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAUG15eF93eWg=,size_20,color_FFFFFF,t_70,g_se,x_16)

## 4.启动 按照图形化界面设置账号密码就可以了

如果启动之后不能 ping 通外网

```
#第一步
cd /etc/sysconfig/network-scripts/

vi ifcfg-enp0s3
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/1a4e21d7d228457098a69a66b15c1182.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAUG15eF93eWg=,size_20,color_FFFFFF,t_70,g_se,x_16)
将 onboot 改为 yes
