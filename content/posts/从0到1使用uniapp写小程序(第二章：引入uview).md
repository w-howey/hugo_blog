---
title: "从0到1使用uniapp写小程序(第二章：引入uview)"
date: 2023-07-26T22:21:16+08:00
draft: true
---

## 安装 scss

uview 依赖于 scss 模块

## 安装 view

下载安装:
登录一下 hbuild，一键导入
[huilder 插件市场-view](https://ext.dcloud.net.cn/plugin?id=1593)

## 配置

项目 main.js 中引入：
![在这里插入图片描述](https://img-blog.csdnimg.cn/3013fa1bef05409cac7c6464801769fe.png)

```js
// main.js
import uView from "@/uni_modules/uview-ui";
Vue.use(uView);
```

**<font color="red">注意这两行要放在 import Vue 之后。</font>**

### 引入 scss

![在这里插入图片描述](https://img-blog.csdnimg.cn/bddc1ad4b8804da38fb5214de5aa13f4.png)

```css
@import "@/uni_modules/uview-ui/theme.scss";
```

### app.vue 引入 基础样式 首行加入 lang="scss"

```css
<style lang="scss">
	/* 注意要写在第一行，同时给style标签加入lang="scss"属性 */
	@import "@/uni_modules/uview-ui/index.scss";
</style>
```

### 配置 easycom 组件模式

打开 pages.json 加入
![在这里插入图片描述](https://img-blog.csdnimg.cn/80bbe7f4bdaf4d1bb8fe10e5ab73bec7.png)

```json
// pages.json
{
  // 如果您是通过uni_modules形式引入uView，可以忽略此配置
  "easycom": {
    "^u-(.*)": "@/uni_modules/uview-ui/components/u-$1/u-$1.vue"
  },

  // 此为本身已有的内容
  "pages": [
    // ......
  ]
}
```

文档链接：
[官方文档链接](https://www.uviewui.com/components/downloadSetting.html)
