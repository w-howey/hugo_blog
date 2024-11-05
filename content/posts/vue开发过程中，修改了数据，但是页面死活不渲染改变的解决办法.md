---
title: "vue开发过程中，修改了数据，但是页面死活不渲染改变！没变化！怎么办？6种方法解决"
date: 2024-06-06T11:21:16+08:00
draft: false
slug: "2407071721"
authors: ["howey"]
tags: ["vue"]
series: ["编程系列"]
categories: ["前端"]
---
## vue开发过程中，修改了数据，但是页面死活不渲染改变！没变化！怎么办？6种方法解决
### 方法1（推荐）：用`JSON.parse(JSON.stringify(objectOrArray))`
通常是某个渲染的数组改变了层级较深的数据导致页面没有实时渲染
就这么写  `this.items=JSON.parse(JSON.stringify(this.items));`

### 方法2：用`:key`
给没有渲染改变数据的html元素加入`:key="update"`
定义一个`update:false`,每次修改数据的时候在后面加一句`this.update=!this.update;`就可以刷新渲染了 

### 方法3：用$set
```js
data() {
    return {
        d: { a: "旧的值" }
    };
},
this.$set(this.d,"a","新的值");
```

### 方法4：用 `$forceUpdate`
在修改数据之后加入`this.$forceUpdate();`即可 

### 方法5：用 `v-if`
 就是给需要刷新数据点html标签加上`v-if`，让其重新渲染（笨办法）

### 方法6（极其不推荐）：用`location.replace("");`
直接重新`location.replace("");` 刷新整个网页