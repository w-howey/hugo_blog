+++ 
draft = false
date = 2023-08-05T21:19:01+08:00
title = "如何完全卸载chrome？"
description = "如何完全卸载chrome？"
slug = "23080521"
authors = ["howey"]
tags = ["工具"]
categories = ["后端"]
externalLink = ""
series =["编程系列"]
+++

# 如何完全卸载 chrome？

## 新建文本文档，复制一下内容，改名为 remove.reg

```
 Windows Registry Editor Version 5.00

    ; WARNING, this file will remove Google Chrome registry entries
    ; from your Windows Registry. Consider backing up your registry before
    ; using this file: http://support.microsoft.com/kb/322756

    ; To run this file, save it as 'remove.reg' on your desktop and double-click it.

    [-HKEY_LOCAL_MACHINE\SOFTWARE\Classes\ChromeHTML]
    [-HKEY_LOCAL_MACHINE\SOFTWARE\Clients\StartMenuInternet\chrome.exe]
    [HKEY_LOCAL_MACHINE\SOFTWARE\RegisteredApplications]
    "Chrome"=-

    [-HKEY_CURRENT_USER\SOFTWARE\Classes\ChromeHTML]
    [-HKEY_CURRENT_USER\SOFTWARE\Clients\StartMenuInternet\chrome.exe]
    [HKEY_CURRENT_USER\SOFTWARE\RegisteredApplications]
    "Chrome"=-

    [-HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Uninstall\Chrome]
    [-HKEY_CURRENT_USER\Software\Google\Update\Clients\{8A69D345-D564-463c-AFF1-A69D9E530F96}]
    [-HKEY_CURRENT_USER\Software\Google\Update\ClientState\{8A69D345-D564-463c-AFF1-A69D9E530F96}]

    [-HKEY_CURRENT_USER\Software\Google\Update\Clients\{00058422-BABE-4310-9B8B-B8DEB5D0B68A}]
    [-HKEY_CURRENT_USER\Software\Google\Update\ClientState\{00058422-BABE-4310-9B8B-B8DEB5D0B68A}]

    [-HKEY_LOCAL_MACHINE\SOFTWARE\Google\Update\ClientStateMedium\{8A69D345-D564-463c-AFF1-A69D9E530F96}]
    [-HKEY_LOCAL_MACHINE\SOFTWARE\Google\Update\Clients\{8A69D345-D564-463c-AFF1-A69D9E530F96}]
    [-HKEY_LOCAL_MACHINE\SOFTWARE\Google\Update\ClientState\{8A69D345-D564-463c-AFF1-A69D9E530F96}]

    [-HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Google\Update\Clients\{8A69D345-D564-463c-AFF1-A69D9E530F96}]


```
