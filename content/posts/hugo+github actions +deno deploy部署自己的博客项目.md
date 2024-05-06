---
title: "hugo + github actions + deno deploy部署自己的博客项目"
date: 2023-08-05T12:20:16+08:00
draft: false
slug: "2307262210"
authors: ["howey"]
series: ["编程系列"]
categories: ["前端"]
tags: ["hugo", "github", "deno deploy"]
---

# 使用 hugo+github actions +deno deploy 部署自己的博客项目

## 第一步：安装 golang 环境

## 第二步：安装 hugo

## 第三步：git 新建两个仓库

1. ### 仓库 1 名称为：`xxxx.github.io` xxx 为自己随便起的名称 简单就好
2. ### 配置 pages
   ### 进入仓库选择上方 settings 然后选择 pages 然后选择分支然后 save

## 第三步：在另外一个仓库（源码仓库）action 重点：

```yml
name: deploy

on:
  push:
  workflow_dispatch:
  schedule:
    # Runs everyday at 8:00 AM
    - cron: "0 0 * * *"

jobs:
  build:
    runs-on: ubuntu-latest
    #权限很重要 不然depoly没权限
    permissions:
      id-token: write # Allows authentication with Deno Deploy.
      contents: read # Allows cloning the repo.
    steps:
      - name: Checkout
        uses: actions/checkout@v2
        with:
          submodules: recursive
          fetch-depth: 0

      - name: Setup Hugo
        uses: peaceiris/actions-hugo@v2
        with:
          hugo-version: "latest"
          extended: true
      #实际编译命令
      - name: Build Web
        run: hugo --minify

      #将打包的public目录下面的内容发布到github pages （国内访问慢）
      - name: Deploy Web
        uses: peaceiris/actions-gh-pages@v3
        with:
          deploy_key: ${{ secrets.HUGO_BLOG_PRIVATE }} #采用ssh方式配置的公私密钥
          #发布到github pages 仓库
          EXTERNAL_REPOSITORY: "w-howey/w-howey.github.io"
          PUBLISH_BRANCH: master
          cname: github.com
          PUBLISH_DIR: "./public"
          commit_message: ${{ github.event.head_commit.message }}
          allow_empty_commit: true

      #将打包的public目录下面的内容发布到 Deno Deploy （在哪都可以访问）
      - name: Deploy to Deno Deploy
        uses: denoland/deployctl@v1
        with:
          project: howey # the name of the project on Deno Deploy
          entrypoint: https://deno.land/std@0.140.0/http/file_server.ts
          root: public # Where the built HTML/CSS/JS files are located.
```
