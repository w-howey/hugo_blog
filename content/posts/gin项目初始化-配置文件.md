---
title: "gin 项目初始化之配置文件"
date: 2023-07-26T22:21:16+08:00
draft: true
---

## 配置文件

### 安装 yaml 解析包

```shell
go get gopkg.in/yarm.v2
```

1. 项目目录下新建`settings.yaml`

配置如下：

```yml
app:
  page_size: 10
  jwt_serret: 23347$040412
  run_mode: debug
server:
  host: "0.0.0.0"
  port: 8080
  env: dev
  read_timeout: 60
  write_timeout: 60
logger:
  level: info
  prefix: "[nlkj_info]"
  director: log
  show-line: true
  log-in-console: true
mysql:
  host: 127.0.0.1
  port: 3306
  db: nlkj_db
  table_prefix: blkj_
  user: root
  password: 123456
  log_level: dev
```

2. 目录下新建 config/config.go 文件

   ```go
   package config

   type Config struct {
   	App    App
   	Server Server
   	Logger Logger
   	Mysql  Mysql
   }
   ```

3. 目录下新建 config/mysql.go,config/app.go,config/server.go,config/logger.go 文件 ,以 mysql 为例

   ```go
   package config

   type Mysql struct {
   	Host        string `yaml:"host"`
   	Port        int    `yaml:"port"`
   	DB          string `yaml:"db"`
   	TablePrefix string `yaml:"table_prefix"`
   	User        string `yaml:"user"`
   	Password    string `yaml:"password"`
   	LogLevel    string `yaml:"log_level"`
   }
   ```

4. 配置全局变量,新建 golbal/golbal.go 文件

```go
package global

import "gin_vue_blog/config"

var Config *config.Config
```

5. 新建 core/conf.go 文件

```go
package core

import (
	"fmt"
	"gin_vue_blog/config"
	"gopkg.in/yaml.v2"
	"io/ioutil"
	"log"
)

// 读取yarm文件配置
func InitConfig() {

	const ConfigFile = "settings.yaml"
	c := &config.Config{}
	file, err := ioutil.ReadFile(ConfigFile)
	if err != nil {
		log.Fatal("配置文件加载失败%s", err)
	}
	err = yaml.Unmarshal(file, c)
	if err != nil {
		log.Fatal("配置文件加载失败%s", err)
	}
	log.Printf("读取成功")
	global.Config = c
	fmt.Println(c)

}

```

## 测试（main.go）

```go
package main

import "gin_vue_blog/core"

func main() {
	core.InitConfig()
}

```
