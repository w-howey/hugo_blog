---
title: "hadoop集群搭建（二）之集群配置"
date: 2023-07-26T22:21:16+08:00
draft: false
tags: ["hadoop", "shell"]
series: ["编程系列"]
categories: ["大数据"]
---

# hadoop 集群搭建（二）之集群配置

---

@[TOC](文章目录)

---

## 一、集群部署规划

> 1.NameNode 和 SecoundNameNode 不要安装在同一台服务器
> 2.ResourceManager 也很消耗内存，不要和 NameNode、SecoundNameNode 配置在同一台机器上

|      | hadoop102         | hadoop103                   | hadoop104                |
| ---- | ----------------- | --------------------------- | ------------------------ |
| HDFS | NameNode DataNode | DataNode                    | SecoundNameNode DataNode |
| yarn | NodeManager       | ResourceManager NodeManager | NodeManager              |

## 二、修改配置文件

### 1. /opt/module/hadoop-3.3.1/etc/hadoop/core-site.xml

```xml
<configuration>
<!--指定nodename地址 -->
	<property>
		<name>fs.defaultFS</name>
		<value>hdfs://hadoop102:8020</value>
	</property>
	<!-- ָ指定hadoop数据的存储目录 -->
	<property>
		<name>hadoop.tmp.dir</name>
		<value>/opt/module/hadoop-3.3.1/data</value>
	</property>
	<!-- 配置hsfs网页登录使用的静态用户-->
	<!-- <property> -->
		<!-- <name>hadoop.http.staticuser.user</name> -->
		<!-- <value>howey</value> -->
	<!-- </property> -->


</configuration>

```

### 2. /opt/module/hadoop-3.3.1/etc/hadoop/hdfs-site.xml

```xml
<configuration>
<!-- nn 外部访问地址-->
	<property>
		<name>dfs.namenode.http-address</name>
		<value>hadoop102:9870</value>
	</property>
	<!-- 2nn 外部访问地址-->
	<property>
		<name>dfs.namenode.secondary.http-address</name>
		<value>hadoop104:9868</value>
	</property>
</configuration>
```

### 3. /opt/module/hadoop-3.3.1/etc/hadoop/yarn-site.xml

```xml
<configuration>
	<!-- nn 指定mr走shuffle-->
	<property>
		<name>yarn.nodemanager.aux-services</name>
		<value>mapreduce_shuffle</value>
	</property>
		<!-- 指定resourcemanager的地址-->
	<property>
		<name>yarn.resourcemanager.hostname</name>
		<value>hadoop103</value>
	</property>
	<!-- 指定环境变量的继承-->
	<property>
		<name>yarn.nodemanager.env-whitelist</name>
		<value>JAVA_HOME,HADOOP_COMMON_HOME,HADOOP_HDFS_HOME,HADOOP_CONF_DIR,CLASSPATH_PREPEND_DISTCACHE,HADOOP_YARN_HOME,HADOOP_MAPRED_HOME</value>
	</property>
			<!-- 开启日志聚集功能-->
	<property>
		<name>yarn.log-aggregation-enable</name>
		<value>true</value>
	</property>
	<!-- 设置日志聚集服务器地址 -->
	<property>
		<name>yarn.log-server.url</name>
		<value>http://hadoop102:19888/jobhistory/logs</value>
	</property>
	<!-- 设置日志保留时间为7天 -->
	<property>
		<name>yarn.log-aggregation.retain-seconds</name>
		<value>604800</value>
	</property>
</configuration>
```

### 4.历史服务器配置

/opt/module/hadoop-3.3.1/etc/hadoop/mapred-site.xml

```xml
<configuration>
<!-- 指定MapReduce程序运行在Yarn上 -->
	<property>
		<name>mapreduce.framework.name</name>
		<value>yarn</value>
	</property>
<!-- 历史服务器端地址 -->
	<property>
		<name>mapreduce.jobhistory.address</name>
		<value>hadoop102:10020</value>
	</property>
	<!-- 历史服务器web端地址 -->
	<property>
		<name>mapreduce.jobhistory.webapp.address</name>
		<value>hadoop102:19888</value>
	</property>

</configuration>
```

### 4.配置 works 文件：

`/opt/module/hadoop-3.3.1/etc/hadoop/workers`
![在这里插入图片描述](https://img-blog.csdnimg.cn/a97752387fa6493da7d1ff24f4d46ea8.png)

### 5.进行集群配置文件分发

## 三、启动

`注意：`第一次启动需要格式化，一定要注意自己使用的那个账号在操作，不要一会 root 一会普通账号
检查`/opt/module/hadoop-3.3.1/data`文件夹的文件所属
格式化之前先删除`/opt/module/hadoop-3.3.1/logs`和`/opt/module/hadoop-3.3.1/data`
格式化：
`hdfs namenode -format`
启动：
`/opt/module/hadoop-3.3.1/sbin/start-dfs.sh`
停止：
`/opt/module/hadoop-3.3.1/sbin/stop-dfs.sh`
检查：
`jps`

根据顶部图可判断需要在服务器 103 上启动 yarn
`/opt/module/hadoop-3.3.1/sbin/start-yarn.sh`
`mapred --daemon start historyserver`
开启停止各服务组件(kill -9 进程号)
`hdfs --daemon start/stop  namenode/datanode/secondarynamenode`
yarn 的重启服务
`yarn --daemon start/stop  resourcemanager/nodemanager`

web 端查看
HDFS 存储信息
`http://hadoop102:9870/`
yarn 的 resourcemanager
`http://hadoop103:8088/`

## 四、测试

`hadoop fs -mkdir /wcinput`
`hadoop fs -put /opt/software/jdk-8u331-linux-x64.tar.gz  /wcinput`
查看 web
`http://hadoop102:9870/explorer.html#/`
文件存储位置 core-site.xml

![在这里插入图片描述](https://img-blog.csdnimg.cn/95d07e3ce6e449baad6cc1af0c50eb3e.png)
`ll  data/dfs/data/current/BP-15966323-192.168.10.102-1650898590994/current/finalized/subdir0/subdir0/
`

表示三个副本
![在这里插入图片描述](https://img-blog.csdnimg.cn/cb9f1b9e4efe4f7c92991b44b4839da7.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAUG15eF93eWg=,size_20,color_FFFFFF,t_70,g_se,x_16)

## 五、集群崩溃处理

1.  停止 yarn 服务
2.  停止 hdfs 服务
3.  删除所有服务器节点 /opt/module/hadoop-3.3.1/logs 和/opt/module/hadoop-3.3.1/data 文件夹
4.  格式化`hdfs namenode -format`
5.  开启 hdfs 服务
6.  开启 yarn 服务

## 六、hadoop 常用脚本

```shell
#!/bin/bash
#集群启停脚本

if [ $# -lt 1 ]
then
	echo "no args input..."
	exit;
fi

case $1 in
	"start" )
		echo "============================启动hadoop集群================="
		echo "============================启动hdfs======================"
		ssh hadoop102 "/opt/module/hadoop-3.3.1/sbin/start-dfs.sh"
		echo "============================启动yarn======================"
		ssh hadoop103 "/opt/module/hadoop-3.3.1/sbin/start-yarn.sh"
		echo "======================启动historyserver======================"
		ssh hadoop102 "/opt/module/hadoop-3.3.1/bin/mapred --daemon start historyserver"
		;;
	"stop" )
		echo "=========================关闭hadoop集群================="
		echo "======================关闭historyserver==================="
		ssh hadoop102 "/opt/module/hadoop-3.3.1/bin/mapred --daemon stop historyserver"
		echo "============================关闭yarn======================"
		ssh hadoop103 "/opt/module/hadoop-3.3.1/sbin/stop-yarn.sh"
		echo "============================关闭hdfs======================"
		ssh hadoop102 "/opt/module/hadoop-3.3.1/sbin/stop-dfs.sh"
		;;
	* )
		echo "input args error...."
	;;
esac
```

```shell
#!/bin/bash
#查看集群是否启动成功

for host in hadoop102 hadoop103 hadoop104
do
	echo =========================$host================
	ssh $host jps
done
```

## 七、hadoop 常用端口号

> **hadoop3.x**
> hdfs namenode 内部常用端口：8020/9000/9820
> hdfs namenode 对用户的查询端口：9870
> yarn 查看任务运行情况的端口：8088
> 历史服务器：19888
> **hadoop2.x**
> hdfs namenode 内部常用端口：8020/9000
> hdfs namenode 对用户的查询端口：50070
> yarn 查看任务运行情况的端口：8088
> 历史服务器：19888
> 常用配置文件
> **hadoop3.x**
> core-site.xml hdfs-site.xml yran-site.xml mapred-site.xml workers
> **hadoop2.x**
> core-site.xml hdfs-site.xml yran-site.xml mapred-site.xml slaves

## 八、集群时间同步

### 注意

生产环境：如果服务器能连接外网，不需要时间同步，会同步外网的时间
生产环境：如果服务器不能连接外网，需要时间同步

### （1）102 机器配置（选择一个作为时间同步的主机）

切换 root 账号
`su root`
查看状态
`systemctl status ntpd`
开启
`systemctl start ntpd`
查看开机自启状态
`systemctl is-enabled  ntpd`
修改配置文件
`vim /etc/ntp.conf`

![在这里插入图片描述](https://img-blog.csdnimg.cn/fce9dc0b505e4589b8863a698dd6344b.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAUG15eF93eWg=,size_20,color_FFFFFF,t_70,g_se,x_16)
在最后追加

```
server 127.127.1.0
fudge 127.127.1.0 stratum 10
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/4d08783bd7754b43b1e57bee6d1d25a4.png)
修改/etc/sysconfig/ntpd 文件
`vim /etc/sysconfig/ntpd`
增加以下内容（让硬件时间与系统时间一起同步）
`SYNC_HWCLOCK=yes`
重启 ntpd 服务
`systemctl start ntpd`
设置 ntpd 服务开机自启
`systemctl enable ntpd`

### （2）其他机器配置

#### 1.关闭所有节点的 ntp 服务和自启动

`systemctl stop ntpd`
`systemctl disable ntpd`

#### 2.一分钟与时间服务器选择的主机做一次时间同步

`crontab -e`
`* * * * * 从左到右依次是 分时日月周`
添加定时任务如下

```crontab
*/1 * * * *   /usr/sbin/ntpdate hadoop102
```

测试：
`date -s "2022-02-22 22:22:22"`
一分钟后查看是否同步
`date`
