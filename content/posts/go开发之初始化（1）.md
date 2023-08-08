---
title: "Go开发之初始化（1）"
date: 2023-08-07T16:44:46+08:00
draft: false
slug: "2308071644"
authors: ["howey"]
tags: ["Go"]
series: ["编程系列"]
categories: ["后端"]
---

# Hello World ！
1. 创建文件夹xxx
2. `go mod init xxx`
3. 创建文件main.go
4. 输出 hello World！
```go
package main

import "fmt"

func main() {
	fmt.Println("hello world!")
}
```
# 建立web服务器

```go
package main

import (
	"fmt"
	"log"
	"net/http"
	"strings"
)

func sayhelloName(w http.ResponseWriter, r *http.Request) {
	r.ParseForm()       //解析参数，默认是不会解析的
	fmt.Println(r.Form) //这些信息是输出到服务器端的打印信息
	fmt.Println("path", r.URL.Path)
	fmt.Println("scheme", r.URL.Scheme)
	fmt.Println(r.Form["url_long"])
	for k, v := range r.Form {
		fmt.Println("key:", k)
		fmt.Println("val:", strings.Join(v, ""))
	}
	fmt.Fprintf(w, "Hello astaxie!") //这个写入到w的是输出到客户端的
}
func main() {
	http.HandleFunc("/", sayhelloName)       //设置访问的路由
	err := http.ListenAndServe(":9090", nil) //设置监听的端口
	if err != nil {
		log.Fatal("ListenAndServe: ", err)
	}
}
```


