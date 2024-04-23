---
title = "windows如何取消桌面图标右下角带快捷方式？"
date = 2023-08-05T21:42:55+08:00
draft = false
description = "windows如何取消桌面图标右下角带快捷方式？"
slug = "2308052141"
authors = ["howey"]
tags = ["工具"]
categories = ["后端"]
externalLink = ""
series =["编程系列"]
---

# windows 如何取消桌面图标右下角带快捷方式？

## 新建文本文档，复制一下内容，改名为 xxx.bat

```bat
reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Shell Icons" /v 29 /d "%systemroot%\system32\imageres.dll,197" /t reg_sz /f
taskkill /f /im explorer.exe
attrib -s -r -h "%userprofile%\AppData\Local\iconcache.db"
del "%userprofile%\AppData\Local\iconcache.db" /f /q
start explorer
pause
```
