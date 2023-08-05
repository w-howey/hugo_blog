---
title: "HDFS常用API操作"
date: 2023-07-26T22:21:16+08:00
draft: false
tags: ["hadoop", "HDFS"]
series: ["编程系列"]
categories: ["大数据"]
---

## 1.下载 winutils.exe

`https://codeload.github.com/tangchunbo/apache-hadoop-3.1.1-winutils/zip/refs/heads/master`
然后解压将文件拷贝到 bin 目录下
`D:\Java\hadoop-3.3.1\bin`
依赖导入

```xml
    <dependencies>
        <dependency>
            <groupId>org.apache.hadoop</groupId>
            <artifactId>hadoop-client</artifactId>
            <version>3.1.3</version>
        </dependency>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-log4j12</artifactId>
            <version>1.7.30</version>
        </dependency>
    </dependencies>

```

```java
package com.howey.hdfs;


import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.*;
import org.junit.After;
import org.junit.Before;
import org.junit.Test;

import java.io.IOException;
import java.net.URI;
import java.net.URISyntaxException;
import java.util.Arrays;

/**
 * 1.获取客户端对象
 * 2.执行相关的操作命令
 * 3.关闭资源
 */
public class HdfsClient {


    private FileSystem fs;

    @Before
    public void init() throws URISyntaxException, IOException, InterruptedException {
        URI uri = new URI("hdfs://hadoop102:8020");
        //创建一个配置文件
        Configuration entries = new Configuration();

        //设置副本
        entries.set("dfs.replication", "2");
        String user = "howey";
        //获取客户端对象
        fs = FileSystem.get(uri, entries, user);
    }

    @After
    public void close() throws IOException {
        //关闭资源
        fs.close();
    }

    //创建目录
    @Test
    public void testMkdir() throws IOException {
        //创建一个文件夹
        fs.mkdirs(new Path("/howey1"));

    }


    //上传文件

    /**
     * 参数优先级
     * hdfs-default.xml<服务器的hdfs-site.xml<用户自定义的hdfs-site.xml<代码配置
     *
     * @throws IOException
     */
    @Test
    public void testUpload() throws IOException {
        //参数1表示删除本地原数据，参数2是否允许覆盖hdfs上面的文件3.原数据路径4.目的地路径
        fs.copyFromLocalFile(false, true,
                new Path("C:\\Users\\Administrator\\Desktop\\test\\js\\bootstrap-datetimepicker.min.js"),
                new Path("/howey/js/bootstrap-datetimepicker.min.js"));
    }

    //下载
    @Test
    public void testGet() throws IOException {
        //参数1表示hdfs原文件是否删除，参数2hdfs上面的文件3.目标地址路径4.参数是否校验
        fs.copyToLocalFile(false, new Path("/howey/js"), new Path("C:\\Users\\Administrator\\Desktop\\test\\js\\"), true);
    }

    //删除
    @Test
    public void testDel() throws IOException {
        //参数1:hdfs的path（文件夹或者目录，非空文件夹需要递归删除，不然会报错） 参数2是否递归删除
        fs.delete(new Path("/howey/js"), false);
    }

    //更名
    @Test
    public void testRenameAndMv() throws IOException {
        //参数1：hdfs文件，参数2：目标文件的文件路径
        Path bootPath = new Path("/howey/bootstrap");
        //文件夹存在吗？不存在创建
        if (!fs.exists(bootPath)) {
            fs.mkdirs(bootPath);
        }
        fs.rename(new Path("/howey/js/bootstrap-datetimepicker.js"), new Path("/howey/bootstrap/bootstrap-datetimepicker.js"));
    }

    @Test
    public void fileDetail() throws IOException {
        //参数1：文件地址，参数二是否递归
        RemoteIterator<LocatedFileStatus> listFiles = fs.listFiles(new Path("/howey"), true);
        while (listFiles.hasNext()) {

            LocatedFileStatus info = listFiles.next();
            Path path = info.getPath();
            System.out.println("========" + path);
            System.out.println("========" + info.getGroup());
            System.out.println("========" + info.getOwner());
            System.out.println("========" + info.getBlockSize());
            System.out.println("========" + info.getLen());
            System.out.println("========" + info.getPermission());
            System.out.println("========" + info.getReplication());

            BlockLocation[] blockLocations = info.getBlockLocations();

            System.out.println("========" + Arrays.toString(blockLocations));
        }

    }

    //文件的判断
    @Test
    public void judgeFileStatus() throws IOException {
        FileStatus[] fileStatuses = fs.listStatus(new Path("/"));
        for (FileStatus status : fileStatuses) {
            String name = status.getPath().getName();
            if (status.isFile()) {
                System.out.println("==========" + name + " is a file");
            } else {
                System.out.println("==========" + name + " is not a file");
            }
        }
    }


}

```
