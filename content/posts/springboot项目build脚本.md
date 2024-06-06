---
title: "springboot项目build脚本"
date: 2024-06-06T15:21:16+08:00
draft: false
slug: "2406061521"
authors: ["howey"]
tags: ["bat","springBoot"]
series: ["编程系列"]
categories: ["后端"]
---
### 在项目文件下新建build.bat文件
```bat
@echo off
echo Starting Maven package...
call mvn clean package
echo Packaging complete.

setlocal enabledelayedexpansion
set "DEST_DIR=./build/"

if not exist "%DEST_DIR%" mkdir "%DEST_DIR%"

if not exist target (
    echo target目录不存在，请确保项目已经正确编译。
    exit /b
)
REM 遍历target目录下的所有.jar文件并移动到build目录下
for %%f in (target\*.jar) do (
    copy "%%f" build)

echo 打包完成，所有.jar文件已移动到build目录下。

echo All JARs moved successfully.
endlocal

```
