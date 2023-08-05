---
title: "laravel+tp项目如何上线以及ssl证书的配置"
date: 2023-07-26T22:21:16+08:00
draft: false
---

### 写完的项目如何部署上线呢？

#### 前言：前几天的文章有写到虚拟机的安装那一块，配置了 nginx，mysql，和 php

##### （一）域名解析及 ssl 证书的申请

以腾讯云为例：

1.  进入控制台，域名管理，解析，会进入到另外一个页面
    ![image.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy8xODgyMDk3NS04MjFhN2U3NDFjMDk2N2FhLnBuZw)
2.  添加记录
    ![image.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy8xODgyMDk3NS01ODZlM2FjYjFjNTYzMzY5LnBuZw)
    主机记录：abc
    记录类型：A
    线路类型：默认
    记录值：服务器公网 ip
    其他不用改，直接解析
    `注：`（1）如果你在主机记录第一个框中填入了 www，代表你解析的是一级域名，一级域名只能绑一个项目，也就是你上线后你的域名需要`www.xxxx.com` ,
    （2）解析成二级域名，主机记录框随便写，例如 abc，那么访问就变成了`abc.xxxx.com`
3.  申请 ssl 证书（可要可不要，没有就是 http 连接访问，申请后配置可转换为 https 访问）例如：没有证书：`http://abc.xxxxx.com`加了证书之后`https://abc.xxxx.com`
    证书的申请步骤：
    (1) 控制台，云产品点进 ssl 证书
    ![image.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy8xODgyMDk3NS1hODQ3ZjJhMGFiNzNmYTVmLnBuZw)
    （2）申请免费证书
    ![image.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy8xODgyMDk3NS0yMDc5OTVmZjI1ZmJlZWFlLnBuZw)
    （3）等待审核，审核完后才能下载到本地

##### （二）xshell 连接服务器，配置 nginx

命令：`ssh xxx@xx.xxx.xx.xxx   //xxx:线上服务器用户名可修改 xx.xxx.xx.xxx:服务器公网ip` 然后会弹出一个框再输入线上服务器的密码
`sudo su //管理员权限`
`cd /`返回根目录
`cd etc/nginx/sites-available/   //进入nginx下面`
`ls`
`cp default php  //php是我随意定义的`
`vi php`
laravel 项目将 nginx 做如下配置
![image.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy8xODgyMDk3NS0wNzk0YTNhNDMxOWRlMjg0LnBuZw)
详细代码如下：

```
server {
        listen 80;
        server_name abc.xxxx.com;
        server_tokens off;
        location / {
          return 301 https://$host$request_uri;
        }
}
server {
        listen       443 ssl;
        ssl on;         //开启证书
        ssl_certificate       /opt/Nginx/abc.crt;    //证书位置
        ssl_certificate_key    /opt/Nginx/abc.key;   //key位置
        root /var/www/abc/public;
        index index.html index.htm index.php index.nginx-debian.html;
        server_name abc.xxxx.com;
        location / {
                try_files $uri $uri/ /index.php?$query_string;
        }
        location ~ .php$ {
              include snippets/fastcgi-php.conf;
              fastcgi_pass unix:/run/php/php7.2-fpm.sock;
      }

}
```

`注意：`thinkphp 项目与其不同，路由那里需要重写,默认的是将
上面的这一段

```
 location / {
                try_files $uri $uri/ /index.php?$query_string;
        }
```

改写为

```
#根据框架需求，配置url重写
      location / {
              try_files $uri $uri/ =404;
      }
```

但是 url 模式不同需要重写路由，先将将 URL_MODEL 设置为 2，然后改写为：

```
location / {
if (!-e $request_filename) {
   rewrite  ^(.*)$  /index.php?s=$1  last;
   break;
    }
 }
```

`注：`第二段 location 可以改写为：

```
  location ~ \.php$ {
                try_files $uri /index.php =404;
                fastcgi_split_path_info ^(.+\.php)(/.+)$;
                fastcgi_pass unix:/var/run/php/php7.2-fpm.sock;
                fastcgi_index index.php;
                fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
                include fastcgi_params;
        }
```

配置完成之后按 esc+：键输入`wq`保存并退出 linux 命令`vi vim //打开的方式不同` 注意一下，`q!  //强制退出`
命令：`nginx -t  //检查配置有没有错误`
`service nginx restart  //重启nginx服务`
命令：`cd /`
`cd var`
`ls`
`chmod -R 777 www  //给权限`

##### （三）配置线上数据库

1.  新建 mysql 连接
    ![image.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy8xODgyMDk3NS1jODQ0YmIwODA0Y2M4Y2ZlLnBuZw)
    然后点击 ssh
    ![image.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy8xODgyMDk3NS1hNDQwNmVmN2U1MzNkODAzLnBuZw)
2.  新建数据库 名字与本地一致，然后导入 sql 文件，刷新就可以看见表了 ####（四）上传项目
3.  将项目打包成 zip 格式
4.  打开 FileZilla Client 软件，这个网上直接下载很方便的
5.  ![image.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy8xODgyMDk3NS1hYmJkOGE3YTFmYzliNzg1LnBuZw)
6.  将打包的项目拖到线上站点的/var/www/下面
7.  将下载的 ssl 证书解压打开有一个 Nginx 的文件夹，打开将文件的长名字改为短名字，与之前 nginx 证书位置设置的名字一样，然后压缩 Nginx 文件夹，并拖到/opt/文件夹下面，发现传输失败
8.  回到 xshell 给`chmod -R 777 opt`权限，然后再传输
9.  linux 安装解压缩 命令：`apt-get install unzip`
10. cd 命令进入 www 解压缩`unzip xxx`；然后给权限`chmod -R 777 xxx`
11. cd 命令进入 opt，解压缩证书,然后删除压缩包
12. cd 进入项目文件 命令：`vi .env`对 env 文件里面的本地数据改为线上的例如数据库等等其他环境变量；

#### （五）测试访问 ok！！！

#### （六）线上线下开启自动同步：修改线下的代码让线上与之同步

点击 Configuration
![image.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy8xODgyMDk3NS01YmU4M2UxODI4ZWU0NDFmLnBuZw)
做如下配置
![image.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy8xODgyMDk3NS1hYmZjOGRiYzY3MTMyNTdkLnBuZw)
勾选自动上传
![image.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy8xODgyMDk3NS0wMjg5ZDkwZjkyYjRjMDdjLnBuZw)
取消勾选
![image.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy8xODgyMDk3NS01OTRmYWUxZWEwZWU3ZDhhLnBuZw)
