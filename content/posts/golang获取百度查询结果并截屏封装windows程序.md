---
title: "golang获取百度查询结果并截屏封装windows程序"
draft: false
date: 2023-09-02T15:59:02+08:00
description: "golang获取百度查询结果并截屏封装windows程序"
slug: "2309091559"
authors: ["howey"]
tags: ["golang"]
categories: ["后端"]
externalLink: ""
series: ["编程系列"]
---

# 获取百度查询结果并截屏保存图片，无需打开浏览器

## 源码

```golang
package main

import (
	"fmt"
	"fyne.io/fyne/v2"
	"fyne.io/fyne/v2/app"
	"fyne.io/fyne/v2/container"
	"fyne.io/fyne/v2/data/binding"
	"fyne.io/fyne/v2/layout"
	"fyne.io/fyne/v2/widget"
	"github.com/flopp/go-findfont"
	"github.com/go-vgo/robotgo"
	"github.com/kbinani/screenshot"
	_ "image/jpeg"
	"image/png"
	_ "image/png"
	"io/ioutil"
	"os"
	"os/exec"
	"strings"
	"time"
)

var CACHEPATH = "./cache"
var IMGPATH = "./resultImage"
var CACHEFILE = "scree.cache"
var DEFAULTWAIT = "3"

func init() {
	//设置中文字体:解决中文乱码问题
	fontPaths := findfont.List()
	for _, path := range fontPaths {
		if strings.Contains(path, "msyh.ttf") || strings.Contains(path, "simhei.ttf") || strings.Contains(path, "simsun.ttc") || strings.Contains(path, "simkai.ttf") {
			os.Setenv("FYNE_FONT", path)
			break
		}
	}
}

func main() {
	//定义搜索内容
	AppMain()
}

func AppMain() {
	myApp := app.New()
	myWindow := myApp.NewWindow("桌面应用程序")
	myWindow.Resize(fyne.NewSize(300, 400))

	cachePath := getCacheChromePath()

	input1 := widget.NewEntry()
	input2 := widget.NewEntry()
	input2.SetText(cachePath)
	input3 := widget.NewEntry()
	input3.SetText(DEFAULTWAIT)
	// 添加 NewEntry 到 GridLayout
	label1 := widget.NewLabel("请输入查询内容(多个查询内容请以英文分号隔开(;)):")
	label5 := widget.NewLabel("示例：query1;query2")
	label2 := widget.NewLabel("请输入chrome的位置:")
	label3 := widget.NewLabel("请输入等待时长（秒）:")
	errMsg := ""
	str := binding.NewString()
	str.Set(errMsg)
	label4 := widget.NewLabelWithData(str)

	cancel := widget.NewButton("取消", func() {
		myApp.Quit()
	})

	confirm := widget.NewButton("确定", func() {
		query := strings.TrimSpace(input1.Text)
		chromePath := strings.TrimSpace(input2.Text)
		waitSecond := strings.TrimSpace(input3.Text)

		errMsg = checkMain(query, chromePath)

		if errMsg != "" {
			str.Set(errMsg)
			return
		}
		if len(waitSecond) == 0 {
			waitSecond = DEFAULTWAIT + "s"
		} else {
			waitSecond = waitSecond + "s"
		}
		waitSecondDur, err := time.ParseDuration(waitSecond)
		if err != nil {
			str.Set("无法解析等待时间")
			return
		}
		//查询
		searchBefore(query, chromePath, waitSecondDur)
		fmt.Println("查询内容:", query)
		fmt.Println("Chrome位置:", chromePath)
		fmt.Println("等待时长:", waitSecond)
		fmt.Println("等待时长:", waitSecondDur)
		myApp.Quit()
	})

	buttons := container.NewHBox(
		layout.NewSpacer(),
		cancel,
		layout.NewSpacer(),
		confirm,
		layout.NewSpacer(),
	)

	content := container.NewVBox(
		layout.NewSpacer(),
		label1,
		label5,
		input1,
		label2,
		input2,
		label3,
		input3,
		label4,
		layout.NewSpacer(),
		buttons,
	)

	myWindow.SetContent(content)
	myWindow.CenterOnScreen()
	myWindow.ShowAndRun()
}

func checkMain(query string, filePath string) string {
	if len(query) == 0 {
		return "请填写查询内容"
	}
	err := checkChromePath(filePath)

	return err
}

func checkChromePath(filePath string) string {
	if len(filePath) == 0 {
		return "请填写的chrome位置"
	}
	//转义字符
	filePath = strings.Replace(filePath, "\\", "/", -1)
	fileInfo, err := os.Stat(filePath)
	if err != nil {
		return "填写的chrome位置：无法获取chrome信息"
	}
	fileMode := fileInfo.Mode()
	perm := fileMode.Perm()
	// 73: 000 001 001 001
	flag := perm & os.FileMode(73)

	if uint32(flag) == uint32(73) {
		return "chrome没有权限执行:exec permission"
	}
	cacheChromePath(filePath)
	return ""
}

func searchBefore(query string, chromePath string, waitSecond time.Duration) {
	parts := strings.Split(query, ";") // 使用分号来切割字符串
	for _, part := range parts {
		if len(part) == 0 {
			continue
		}
		searchMain(part, chromePath, waitSecond)
		closeNative()
	}
}

func buildDir(dirPath string) {
	if _, err := os.Stat(dirPath); os.IsNotExist(err) {
		// 如果文件夹不存在，则创建它
		err := os.MkdirAll(dirPath, 0755)
		if err != nil {
			fmt.Println("无法创建文件夹:", err)
			return
		}
		fmt.Println("文件夹已创建:", dirPath)
	}
}

func cacheChromePath(chromePath string) {
	buildDir(CACHEPATH)
	filePath := CACHEPATH + "/" + CACHEFILE
	err := ioutil.WriteFile(filePath, []byte(chromePath), 0644)
	if err != nil {
		fmt.Println("无法写入文件:", err)
		return
	}
	fmt.Println("成功将字符串写入文件:", filePath)
}

func getCacheChromePath() string {
	filePath := CACHEPATH + "/" + CACHEFILE
	_, err := os.Stat(filePath)
	if err != nil {
		return ""
	}
	// 打开文件，第二个参数是打开模式，这里使用只读模式
	file, err := os.Open(filePath)
	if err != nil {
		fmt.Println("无法打开文件:", err)
		return ""
	}
	defer file.Close()
	// 读取文件内容
	content, err := ioutil.ReadAll(file)
	if err != nil {
		fmt.Println("无法读取文件内容:", err)
		return ""
	}
	// 将文件内容转换为字符串并打印出来
	fmt.Println(string(content))
	return strings.TrimSpace(string(content))
}

func searchMain(query string, chromePath string, waitSecond time.Duration) {
	searchArray := []string{"诉讼", "被告", "违法", "融资", "拖欠欠税", "排污", "不良"}
	for index, s := range searchArray {
		search := query + " " + s
		println(search)
		startGoogle(search, chromePath, waitSecond)
		createImage(search)
		if index > 0 {
			closeNative()
		}
	}
}

func startGoogle(search string, chromePath string, waitSecond time.Duration) {
	googleChrome := chromePath
	// 执行命令行命令，打开谷歌浏览器并跳转到搜索结果页面
	cmd := exec.Command(googleChrome, fmt.Sprintf("https://www.baidu.com/s?wd=%s", search))
	err := cmd.Run()
	if err != nil {
		fmt.Println(err)
	}
	time.Sleep(waitSecond)
}

func createImage(filename string) {
	// 获取屏幕的尺寸
	bounds := screenshot.GetDisplayBounds(0)
	// 截屏
	img, err := screenshot.CaptureRect(bounds)
	if err != nil {
		fmt.Println("无法截屏:", err)
		return
	}
	buildDir(IMGPATH)
	fileInfo := IMGPATH + "/" + filename
	// 保存截屏为PNG文件
	outputFile, err := os.Create(fileInfo + ".png")
	if err != nil {
		fmt.Println("无法创建截屏文件:", err)
		return
	}
	defer outputFile.Close()

	err = png.Encode(outputFile, img)
	if err != nil {
		fmt.Println("无法保存截屏:", err)
		return
	}

	fmt.Println("截屏已成功保存到 " + filename + ".png")
}

func closeNative() {
	robotgo.KeyTap("w", "ctrl")
	time.Sleep(1 * time.Millisecond)
}


```

## 编译

```dos
go build   -o scree.exe   -ldflags "-s -w -H=windowsgui"
```
