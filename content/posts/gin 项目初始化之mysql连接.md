---
title: "gin 项目初始化之mysql连接"
date: 2023-07-26T22:21:16+08:00
draft: true
---

# gin 项目初始化之 mysql 连接

## 1. 安装相关包

```shell
go get gorm.io/gorm
go get gorm.io/driver/mysql
```

## 2.修改 mysql 相关配置如下：

```yaml
mysql:
  host: 127.0.0.1
  port: 3306
  db: nlkj_db
  table_prefix: nlkj_
  user: root
  password: 123456
  log_level: dev
  config: "charset=utf8&parseTime=True&loc=Local"
  max_idle_cons: 10
  MaxOpenCons: 100
```

对应 mysql 配置结构体修改如下

```go
package config

import "strconv"

type Mysql struct {
	Host        string `yaml:"host"`
	Port        int    `yaml:"port"`
	DB          string `yaml:"db"`
	TablePrefix string `yaml:"table_prefix"`
	User        string `yaml:"user"`
	Password    string `yaml:"password"`
	LogLevel    string `yaml:"log_level"`
	Config      string `yaml:"config"`
	MaxIdleCons int    `yaml:"max_idle_cons"`
	MaxOpenCons int    `yaml:"max_open_cons"`
}

// 连接url拼接
func (m *Mysql) Dsn() string {
	return m.User + ":" + m.Password + "@tcp(" + m.Host + ":" + strconv.Itoa(m.Port) + ")/" + m.DB + "?" + m.Config
}

```

## 3. 修改 golbal/golbal.go 文件

```go
package global

import (
	"gin_vue_blog/config"
	"gorm.io/gorm"
)

var (
	Config *config.Config
	DB     *gorm.DB
)

```

## 4. 新建 core/gorm.go 文件

```go
package core

import (
	"fmt"
	"gin_vue_blog/global"
	"gorm.io/driver/mysql"
	gorm "gorm.io/gorm"
	"gorm.io/gorm/logger"
	"log"
	"time"
)

func InitMysqlConnect() *gorm.DB {
	if global.Config.Mysql.Host == "" {
		log.Printf("未配置mysql，取消gorm得连接")
		return nil
	}

	var mysqlLogger logger.Interface
	if global.Config.Server.Env == "dev" {
		mysqlLogger = logger.Default.LogMode(logger.Info)
	} else {
		mysqlLogger = logger.Default.LogMode(logger.Error)
	}
	dsn := global.Config.Mysql.Dsn()
	db, err := gorm.Open(mysql.Open(dsn), &gorm.Config{
		Logger: mysqlLogger,
	})
	if err != nil {
		log.Fatal(fmt.Sprintf("[%s]mysql连接失败", dsn))
	}
	sqlDB, _ := db.DB()
	sqlDB.SetMaxIdleConns(global.Config.Mysql.MaxIdleCons) //最大空闲连接数
	sqlDB.SetMaxOpenConns(global.Config.Mysql.MaxOpenCons) //最多可容纳
	sqlDB.SetConnMaxLifetime(time.Hour * 4)                //连接最大服用时间 不能超过mysql得wait_timeout

	global.DB = db
	return db
}

```

## 调用测试 (main.go)

```go
package main

import (
	"fmt"
	"gin_vue_blog/core"
	"gin_vue_blog/global"
)

func main() {
	core.InitConfig()
	core.InitMysqlConnect()
	fmt.Println(global.DB)
}

```
