---
title: "html5+css小知识（2）"
date: 2023-08-07T11:09:26+08:00
draft: false
slug: "2308070926"
authors: ["howey"]
tags: ["H5", "css"]
series: ["编程系列"]
categories: ["前端"]
---

# css篇

## 1. 样式继承 

`<b>`元素继承了`<p>`s元素的样式
```
<p style="color:red;">这是<b>HTML5</b></p>
```
样式继承只适用于元素的外观（文字、颜色、字体等），而元素在页面上的布局样式则不会被继承。如果继承这种样式，就必须使用强制继承：inherit。

`强制继承布局样式`

```
p { 　　border: 1px solid red;
} b { 　　border : inherit;
}
```

## 2. 选择器

### 2-1. 属性选择器

```
//所需 CSS2 版本
[href] { color: orange;
}

[type="password"] { border: 1px solid red;
}

//所需版本 CSS3 属性值开头匹配的属性选择器。
[href^="http"] { color: orange;
}

//所需版本 CSS3 属性值结尾匹配的属性选择器。
[href$=".com"] { color: orange;
}

//所需版本 CSS3 属性值包含指定字符的属性选择器。
[href*="baidu"] { color: orange;
}

//所需版本 CSS2 属性值具有多个值时，匹配其中一个值的属性选择器。
[class~="edf"] { font-size: 50px;
}

//所需版本 CSS2 属性值具有多个值且使用“-”号连接符分割的其中一个值的属性选择器
[lang|="en"] { color: red;
}
```

### 2-2. 复合选择器

```
//相邻兄弟选择器
p + b { color: red;
}

//普通兄弟选择器
p ~ b { color: red;
}
```

### 2-3. 伪元素选择器

```
//::first-line**块级首行**
::first-line { color: red;
}

//::first-letter**块级首字母**
::first-letter { color: red;
}
```