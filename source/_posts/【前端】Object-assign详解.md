---
title: Object.assign详解
date: 2022-05-24 00:04:32
tags: ES6
categories: 前端/移动
---



### 一、Object.assign是什么？

首先了解下Object.assign()是什么。我们先看看ES6官方文档是怎么介绍的？

> Object.assign() 方法用于将所有可枚举属性的值从一个或多个源对象复制到目标对象。它将返回目标对象。

简单来说，就是Object.assign()是对象的静态方法，可以用来复制对象的可枚举属性到目标对象，利用这个特性可以实现对象属性的合并。



### 二、用法：
```javascript
Object.assign(target, ...sources)
```
**参数：**target：目标对象，source：源对象
**返回值：**target，即目标对象