---
title: "虚拟机搭建 hadoop"
date: 2023-07-26T22:21:16+08:00
draft: false
---

# 虚拟机搭建 hadoop

---

@[TOC](文章目录)

---

## 一、搭建 centos 虚拟机，

### 1.分区等设置

下载镜像导入 ，分区：
硬件：内存 2G，硬盘 50G
分区：/boot 挂载 1g
/swap 挂载 4G （内存不够可采用磁盘当内存使用）
/挂载 45G

### 2.网络 ip 设置

**（1）虚拟网络编辑器设置**：选择 vmnet8 子网 ip 设置网段修改为任意网段 例如 10
![在这里插入图片描述](https://img-blog.csdnimg.cn/e5ca05a7565f42f388135c69da874089.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAUG15eF93eWg=,size_20,color_FFFFFF,t_70,g_se,x_16)

**nat 设置**：原则同网段就想
![在这里插入图片描述](https://img-blog.csdnimg.cn/238b7dddf58040628b4e75daffe5a8ff.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAUG15eF93eWg=,size_20,color_FFFFFF,t_70,g_se,x_16)

**（2）本地机设置**：vmnet8 ipv4 属性
![在这里插入图片描述](https://img-blog.csdnimg.cn/7af9d7f02c56467b88bc20034290373c.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAUG15eF93eWg=,size_20,color_FFFFFF,t_70,g_se,x_16)
**（3）虚拟机 centos 系统设置：**

```
vim /etc/sysconfig/network-scripts/ifcfg-ens33
```

如下：
![在这里插入图片描述](https://img-blog.csdnimg.cn/e9c574739ecd4a63a0dc434df966438a.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAUG15eF93eWg=,size_15,color_FFFFFF,t_70,g_se,x_16)
最后重启一下虚拟机。。
ping 一下外网和内网，查看是否能通信，通信就搞定
设置一下 host
`vim /etc/hosts`
![在这里插入图片描述](https://img-blog.csdnimg.cn/9cb97be39c164ccb88252863040fde39.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAUG15eF93eWg=,size_20,color_FFFFFF,t_70,g_se,x_16)

**安装一下软件工具包**
`yum install -y epel-release`
**关闭防火墙**
`systemctl stop firewalld`
**禁止防火墙开机自启**
`systemctl disable firewalld.service`

番外：linux 的权限设置 `/etc/sudoers`
![在这里插入图片描述](https://img-blog.csdnimg.cn/794376df7dd542508fabf1841e49b1c2.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAUG15eF93eWg=,size_17,color_FFFFFF,t_70,g_se,x_16)
`cd /opt/`
`sudo rm -rf rh`
`sudo mkdir module`
`sudo mkdir software`
`sudo chown howey:howey  module/ software/`

## 二、安装 jdk

### 1.卸载自带的 jdk

`rpm -qa |grep -i java |xargs -n1 rpm -e --nodeps`
rpm -qa：查询所安装的所有 rpm 软件包
grep -i ：忽略大小写
xargs -n1 ：表示每次只传递一个参数
rpm -e -nodeps ：强制卸载软件

### 2.下载 jdk for linux sftp

上传到目录`/opt/software/`
解压安装
`tar -zxvf jdk-8u331-linux-x64.tar.gz -C /opt/module/`

### 3 配置系统变量

`sudo vim /etc/profile.d/my_env.sh`
![在这里插入图片描述](https://img-blog.csdnimg.cn/e5a0ac94b6f047ab9f1e64e47aa8ca0f.png)
重新加载资源
`source /etc/profile`
`java -version`

## 二、安装 hadoop

### 1.[下载 hadoop](https://www.apache.org/dyn/closer.cgi/hadoop/common/hadoop-3.3.1/hadoop-3.3.1.tar.gz)

上传到目录`/opt/software/`

### 2.解压安装

`tar -zxvf hadoop-3.3.1.tar.gz  -C /opt/module/`

### 3 配置系统环境变量

![在这里插入图片描述](https://img-blog.csdnimg.cn/0d48fd63fb7f442bb032e089beb12fdf.png)
重新加载资源
`source /etc/profile`
`hadoop version`
