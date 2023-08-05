---
title: "gin项目初始化之logrus配置"
date: 2023-07-26T22:21:16+08:00
draft: false
tags: ["golang", "gin", "logrus"]
series: ["编程系列"]
categories: ["后端"]
---

# gin 项目初始化之 logrus 配置

## 1. 安装 logrus

```shell
go get github.com/sirupsen/logrus
```

## 2. 配置 global

```go
package global

import (
	"gin_vue_blog/config"
	"github.com/sirupsen/logrus"
	"gorm.io/gorm"
)

var (
	Config *config.Config
	DB     *gorm.DB
	Log    *logrus.Logger
)

```

## 3.新建 core/logrus.go 文件

```go
package core

import (
	"bytes"
	"fmt"
	"gin_vue_blog/global"
	"github.com/sirupsen/logrus"
	"os"
	"path"
	"time"
)

const (
	red    = 31
	yellow = 33
	blue   = 36
	gray   = 37
)

type LogFormatter struct {
}

func (t *LogFormatter) Format(entry *logrus.Entry) ([]byte, error) {
	var leveColor int

	switch entry.Level {
	case logrus.DebugLevel, logrus.TraceLevel:
		leveColor = gray
	case logrus.ErrorLevel, logrus.FatalLevel, logrus.PanicLevel:
		leveColor = red
	case logrus.WarnLevel:
		leveColor = yellow
	default:
		leveColor = blue
	}
	var b *bytes.Buffer
	if entry.Buffer != nil {
		b = entry.Buffer
	} else {
		b = &bytes.Buffer{}
	}
	logConfig := global.Config.Logger
	//自定义日期格式
	timestamp := entry.Time.Format(time.DateTime)
	if entry.HasCaller() {
		funcVal := entry.Caller.Function
		fileVal := fmt.Sprintf("%s:%d", path.Base(entry.Caller.File), entry.Caller.Line)
		fmt.Fprintf(b, "[%s][%s] \x1b[%dm[%s]\x1b[0m %s %s %s\n", logConfig.Prefix, timestamp, leveColor, entry.Level, fileVal, funcVal, entry.Message)
	} else {
		fmt.Fprintf(b, "[%s][%s] \x1b[%dm[%s]\x1b[0m %s\n", logConfig.Prefix, timestamp, leveColor, entry.Level, entry.Message)
	}
	return b.Bytes(), nil

}
func InitLogger() *logrus.Logger {
	//新建一个实例
	mLog := logrus.New()
	//设置输出类型
	mLog.SetOutput(os.Stdout)
	//开启返回函数名和行号
	mLog.SetReportCaller(global.Config.Logger.ShowLine)
	//设置自定义得formatter
	mLog.SetFormatter(&LogFormatter{})
	//设置最低得level
	level, err := logrus.ParseLevel(global.Config.Logger.Level)
	if err != nil {
		level = logrus.InfoLevel
	}
	mLog.SetLevel(level)
	InitDefaultLogger()
	global.Log = mLog
	return mLog
}

func InitDefaultLogger() {
	//全局log
	logrus.SetOutput(os.Stdout)
	logrus.SetReportCaller(global.Config.Logger.ShowLine)
	logrus.SetFormatter(&LogFormatter{})
	//设置最低得level
	level, err := logrus.ParseLevel(global.Config.Logger.Level)
	if err != nil {
		level = logrus.InfoLevel
	}
	logrus.SetLevel(level)

}

```
