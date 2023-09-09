+++ 
draft = false
date = 2023-09-02T15:59:02+08:00
title = "golang获取百度查询结果并截屏封装windows程序"
description = "golang获取百度查询结果并截屏封装windows程序"
slug = "2309091559"
authors = []
tags = ["golang"]
categories = ["后端"]
externalLink = ""
series = ["编程系列"]
+++

# 获取百度查询结果并截屏保存图片，无需打开浏览器

## 源码

```golang
package main

import (
	"fmt"
	"fyne.io/fyne/v2"
	"fyne.io/fyne/v2/app"
	"fyne.io/fyne/v2/container"
	"fyne.io/fyne/v2/layout"
	"fyne.io/fyne/v2/widget"
	"github.com/flopp/go-findfont"
	"github.com/kbinani/screenshot"
	_ "image/jpeg"
	"image/png"
	_ "image/png"
	"os"
	"os/exec"
	"strings"
	"time"
)

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

	input1 := widget.NewEntry()
	input2 := widget.NewEntry()
	input3 := widget.NewEntry()
	// 添加 NewEntry 到 GridLayout
	label1 := widget.NewLabel("请输入查询内容:")
	label2 := widget.NewLabel("请输入chrome的位置:")
	label3 := widget.NewLabel("请输入等待时长:")
	errMsg := ""
	label4 := widget.NewLabelWithStyle(errMsg, fyne.TextAlignCenter, fyne.TextStyle{
		Bold:      false, // 是否加粗
		Italic:    false, // 是否斜体
		Monospace: false, // 是否等宽字体
		//Color:     color.RGB(255, 0, 0), // 设置文本颜色为红色
	})
	cancel := widget.NewButton("取消", func() {
		myApp.Quit()
	})

	confirm := widget.NewButton("确定", func() {
		query := input1.Text
		chromePath := input2.Text
		waitSecond := input3.Text
		errMsg = checkChromePath(chromePath)
		if errMsg != "" {
			return
		}
		//setSearch(query)
		fmt.Println("查询内容:", query)
		fmt.Println("Chrome位置:", chromePath)
		fmt.Println("Chrome位置:", waitSecond)
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

func checkChromePath(filePath string) string {
	fileInfo, err := os.Stat(filePath)
	if err != nil {
		fmt.Println("无法获取文件信息:", err)
		return "填写的chrome位置：无法获取文件信息"
	}
	mode := fileInfo.Mode()
	if !(mode.IsRegular() && (mode.Perm()&0111) != 0) {
		fmt.Println(filePath, "不是一个可执行文件")
		return "填写的chrome位置：不是一个可执行文件"
	}
	return ""
}

func setSearch(query string) {
	searchArray := []string{"诉讼", "被告", "违法", "融资", "拖欠欠税", "排污", "不良"}
	for _, s := range searchArray {
		search := query + " " + s
		println(search)
		startGoogle(search)
		createImage(search)
	}
}

func startGoogle(search string) {
	googleChrome := "C:\\Users\\xxx\\AppData\\Local\\Google\\Chrome\\Application\\chrome.exe"
	// 执行命令行命令，打开谷歌浏览器并跳转到搜索结果页面
	cmd := exec.Command(googleChrome, fmt.Sprintf("https://www.baidu.com/s?wd=%s", search))
	err := cmd.Run()
	if err != nil {
		fmt.Println(err)
	}
	time.Sleep(2 * time.Second)
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

	// 保存截屏为PNG文件
	outputFile, err := os.Create(filename + ".png")
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

```
