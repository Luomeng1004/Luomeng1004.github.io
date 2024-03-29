---
title: 深入浅出React和Redux——React的工作方式
date: 2022-05-20 22:58:30
tags: [React]
categories: 专业
---

### React的理念

> 打一个比方，React是一个聪明的建筑工人，而jQuery是一个比较傻的建筑工人，开发者你就是一个建筑的设计师，如果是jQuery这个建筑工人为你工作，你不得不事无巨细地告诉jQuery“如何去做”，要告诉他这面墙要拆掉重建，那面墙上要新开一个窗户，反之，如果是React这个建筑工人为你工作，你所要做的就是告诉这个工人“我想要什么样子”，只要把图纸递给React这个工人，他就会替你搞定一切，当然他不会把整个建筑拆掉重建，而是很聪明地把这次的图纸和上次的图纸做一个对比，发现不同之处，然后只去做适当的修改就完成任务了。



<!--more-->



React的理念，归结为一个公式，就像下面这样：

UI＝render（data）

让我们来看看这个公式表达的含义，用户看到的界面（UI），应该是一个函数（在这里叫render）的执行结果，只接受数据（data）作为参数。这个函数是一个纯函数，所谓纯函数，指的是没有任何副作用，输出完全依赖于输入的函数，两次函数调用如果输入相同，得到的结果也绝对相同。如此一来，最终的用户界面，在render函数确定的情况下完全取决于输入数据。

对于开发者来说，重要的是区分开哪些属于data，哪些属于render，想要更新用户界面，要做的就是更新data，用户界面自然会做出响应，所以React实践的也是“响应式编程”（Reactive Programming）的思想，这也就是React为什么叫做React的原因。



### Virtual DOM

React利用Virtual DOM，让每次渲染都只重新渲染最少的DOM元素。

DOM树是对HTML的抽象，那Virtual DOM就是对DOM树的抽象。Virutal DOM不会触及浏览器的部分，只是存在于JavaScript空间的树形结构，每次自上而下渲染React组件时，会对比这一次产生的Virtual DOM和上一次渲染的Virtual DOM，对比就会发现差别，然后修改真正的DOM树时就只需要触及差别中的部分就行。
