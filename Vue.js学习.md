[TOC]

**Vue学习**

# 工作中遇到的零碎知识

 **$root用法**

- 作用：访问根组件中的属性或方法
- 注意：是根组件，不是父组件。$root只对根组件有用。

this.$root.resMenu["myService_export"]

- myService是菜单的代码
- export是菜单下按钮的代码
- 代码的含义就是判断该角色有无访问该菜单下按钮的权限。

## slot-scope

**slot-scope="scope" 来取得作用域插槽:data绑定的数据，scope可以随便替换其他名称，只是定义对象来代表取得的data数据，便于使用**

```html
<template slot-scope="scope">
   <el-button type="primary" size="small" @click="$router.push(`/categories/edit/${scope.row._id}`)">编辑</el-button>    <!-- $router.push()  跳转页面 -->
   <el-button type="primary" size="small" @click="remove(scope.row._id)">删除</el-button> 
</template>
```



## 工作vue版本

2.5.2

# 前言

vue基础

vue-cli：工程化开发

vue-router： 实现前端路由

vuex：应用复杂时，保存数据

element-ui ：UI组件库

# Vue是什么？

构建用户界面的渐进式的JS框架。

国内前端工程师的必备技能

## Vue特点

- 组件化模式，提高代码的复用率，且让代码更好维护。
- 声明式编码，让编码人缘无需直接操作DOM，提高开发的效率。
  - 声明式：
  - 命令式：
- 使用虚拟DOM+优秀的Diff算法，尽量复用DOM节点

## 基础知识

- ES6语法规范

- ES6模块化

- 包管理器

- 原型、原型链

- 数组常用方法

- axios

- promise

# [尚硅谷Vue2.0+Vue3.0全套教程丨vuejs从入门到精通](https://www.bilibili.com/video/BV1Zy4y1K7SH?p=5)

## 005 Hello小案例

shift+F5（浏览器自带的刷新按钮）  强制刷新

有些资源请求不到之后，刷新后就不再请求，强制刷新保证会再次请求。



配置对象

key不能更改，value的格式也不能更改。

没有配置，实例与容器是相互看不见的。

el：“xxxx” 用于指定当前Vue实例为哪个容器服务。XXXX通常为CSS选择器字符串

data：data用于存储数据，数据供el所指定的容器使用。

```html
const x = new Vue({
    el:"XXXX",  // 用于指定当前Vue实例为哪个容器服务。XXXX通常为CSS选择器字符串
    data:"",    // 用于存储数据，数据供el所指定的容器使用。
})
// {}中的内容为配置对象
```

## 06 分析Hello案例

- 实例与容器是一一对应的关系

- 注意区分JS表达式与JS代码
  - 表达式可以生成值
  - JS代码
  - 简单的理解，表达式可以生成值，赋给其他的语句。代码不行。
- 真实开发中只有一个Vue实例，并且会配合着组件一起使用。

## 048 引出生命周期

动态绑定样式，需要加上：

`<h2 :style="{opacity: opacity}">欢迎学习Vue</h2>`

- 第一个opacity：样式
- 第二个opacity：data中的属性值

在ES6中，当两者重名时，可以简写为`{opacity}`



- 在JS中0.1+0.2不等于0.3
- =>箭头函数不是Vue管理的。
- undefined是不呈现的页面上的
- 修改data中的数据，就会重新解析模板
- vue解析数据变成虚拟DOM，虚拟DOM转成真实DOM，真实DOM挂载到页面。

### mounted

- 完成模板的解析，并把初始的、真实的DOM元素放入页面后（完成挂载）调用mounted。
  - 简单理解，第一次把东西放入页面
- 只调用一次

### 生命周期函数

- 特殊的时间点调用

- 又名：生命周期回调函数。生命周期钩子（程序员之间常用）

- 生命周期函数中的this指向vm或者组件实例对象。

## 049 生命周期-挂载流程

## 050 生命周期-更新流程

- render渲染
