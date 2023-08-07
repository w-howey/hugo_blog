+++ 
draft = false
date = 2023-08-06T19:43:02+08:00
title = "前端vue小知识"
description = "前端vue小知识"
slug = "2308061943"
authors = []
tags = ["vue"]
categories = ["前端"]
externalLink = ""
series = ["编程系列"]
+++

# vue 模版语法

## 插值语法

```vue
{{ name }}
```

## 指令语法

```vue
v-bind:href="url" 简写成 :href="url"
```

`v-bind为单向数据绑定，页面数据修改了data值不会改变`

# 双向绑定

#### `v-model`只能应用在表单内元素上

#### 例如：（错误例子，编译报错）

`<h2 v-model:x="2222">hello</h2>`

#### `v-model:value="name"`可以简写成`v-model="name"`

# el 与 data 的两种写法

el 示例：

```vue
//第一种写法
new Vue({
  el :"#root"

})
//第二种写法
const x=new Vue()
x.$mount("#root")

```

data 有对象式和函数式两种写法，
data 不要使用箭头函数，this 指向外面的 window

```vue
//不适用 
data:()=>{ } 
//以下等价 
data:function(){ } 
data(){ }
```

# mvvm 模型

m:模型：data 数据

v:视图：模板

vm：视图模型：vue 实例

# 数据代理

```js
let person ={
  name:"张三",
  sex:"男",
  //age:18
}

//追加一个属性
Object.defineProperty(person,"age",{
  value:18,
  //控制属性是否可以枚举,默认false
  enumerable:true,
  //控制属性是否可以被修改，默认false
  writabel:true,
  //控制属性是否可以被删除，默认false
  configurable:true


  //
})
let address ="hello"
Object.defineProperty(person,"address",{
  //当读取person的age属性时
  get(){
    return address
  },
  //当修改person的age属性时
  set(value){
      address=value
  }
})
console.log(person)
```

`注意`

```js
let number=18
let person ={
  name:"张三",
  sex:"男",
  age:number
}
console.log(person)
//此时如果number变化,person不会变
number=19
console.log(person)
```

总结：`通过一个对象代理对另一个对象中的属性的操作（读/写），就是数据代理`
