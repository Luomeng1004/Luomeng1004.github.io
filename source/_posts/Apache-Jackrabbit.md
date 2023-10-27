---
title: Apache Jackrabbit
date: 2022-05-20 00:28:41
tags: 数据库
categories: 后端/架构
---

## 什么是Jackrabbit

> JackRabbit就是一种面向文档的数据库，它和MongoDB，CouchDB的功能十分接近，优势在于JackRabbit遵从JCR标准，日后可以迁移到其他同样兼容JCR的性能更好的商业解决方案上。
>
> Jackrabbit（内容仓库）是一个高级的信息管理系统，该系统是传统的数据仓库的扩展，它提供了诸如版本控制、全文检索、访问控制、内容分类、内容事件监视等内容服务。Jackrabbit里面有一个DataStore类，该类有两个实现，DbDataStore和FileDataStore，可以保存元数据和二进制数据。



<!--more-->



## 什么是JCR

Java Content Repository API（JSR-170）试图建立一套标准的API去访问内容仓库。

内容仓库可以理解为一个用来存储文本和二进制数据（图片，word文档，PDF等等）的数据存储应用程序。
你不用关心你真正的数据到底存储在什么地方，是关系数据库？是文件系统？还是XML？
通过JSR-170，你开发代码只需要引用 javax.jcr.* 这些类和接口。它适用于任何兼容JSR-170规范的内容仓库。

**JSR-170 API对不同的人员提供了不同的好处**

- 对于开发者无需了解厂家的仓库特定的API，只要兼容JSR-170就可以通过JSR-170访问其仓库。
- 对于使用CMS的公司则无需花费资金用于在不同种类CMS的内容仓库之间进行转换。
- 对于CMS厂家，无需自己开发内容仓库，而专注于开发CMS应用。



### JCR的内容仓库模型

- JCR的内容仓库是一个树状结构
- 树上的元素（Item）分为两类：节点（node）和属性（property）。
- 1个节点：有且只有一个父亲，有任意数目的孩子（子节点）和任意数目的属性。
- 1个属性：有且只有一个父亲（节点），它没有子节点，由一个名字和一个或多个值组成。
- 属性值的类型可以是：布尔（Boolean）、日期（Date）、双精度（Double）、长整型（Long）、字符串（String）或流（Stream）。
- 属性可以被用来存储信息，节点则被用来创建树内部的“路径”（类似文件系统的结构，节点是目录，属性是实际的文件）

<img src="http://img.boomclap.cn/uPic/202205/1653137914772zC6aqK.png" alt="image-20220521205834607" style="zoom:50%;" />

​		从上面的图中不难发现，根节点下有多个子节点a、b、c，每个子节点下面又会有多个子节点或属性。例如：a节点下有两个子节点d和e，而e含有两个属性节点j和k，属性j包含了一幅图片，属性k为一个浮点数字；同样的属性g包含了一段字符串而属性h则包含了一个整型数字。

　　上面图中的每个节点都可以通过他们在层次结构中的绝对路径来唯一标识。例如：“/”可以定位到根节点，而路径/a/d/i则引用了值为“ture”的属性 i。同时绝对路径总是以“/”开始的，而相对路径则是以层次中的某个节点为参考物的。例如：相对于/a而言，我们可以通过d/i来定位到值为“true” 的属性i。

从对象关系角度上看，因为节点和属性含有很多共性的同时又有各自的特点，因而他们在扩展了Item接口的同时增加了自己独特的方法。我们可以用UML图（1-3）来表示他们之间的关系：

![img](http://img.boomclap.cn/uPic/202205/1653137789714eLYcJV.jpg)

从UML图中我们不难看出，Node和Property都是Item的子类，每个Property节点有且只有一个Node类型的父节点，而每个Node节点只能有0个（根节点）或一个Node父节点，以及多个Item子节点。



### JCR API

使用JCR API时，为了更容易的完成JCR更换，同时尽可能的减少代码变动，建议使用来自javax.jcr包的接口。

Jcr的包结构介绍如下表：

| 包名                   | 功能                         |
| ---------------------- | ---------------------------- |
| Javax.jcr              | java技术下内容管理的接口和类 |
| Javax.jcr.lock         | 内容管理锁功能需要的接口和类 |
| Javax.jcr.nodetype     | 对内部节点操作相关的功能     |
| Javax.jcr.obeservation | 事件订阅处理相关的功能       |
| Javax.jcr.query        | 内容查询相关的功能           |
| Javax.jcr.query.qom    | 内容查询的对象模型定义       |
| Javax.jcr.retention    | 保持管理相关的功能           |
| Javax.jcr.security     | 访问控制管理相关的功能       |
| Javax.jcr.util         | 通用帮助类                   |
| Javax.jcr.version      | 版本控制相关的功能           |

在jcr中，一个Repository对象代表了整个仓库，客户端可以通过Repository.login方法连接到仓库，连接时可以指定一个工作空间和相关凭证。Login方法返回一个Session对象，它代表客户端和仓库之间的连接，该对象同时还封装了登录用户的授权集合以及到可访问工作空间的绑定。工作空间与Session之间是一一对应的关系，我们可以把工作空间看作是当前用户授权集合下能够访问到的内容实体的视图。



常用的接口

- Node Session.getRootNode()：获取根节点，通过该节点可以访问该Session对象有权限访问的所有节点
- Node Node.getNode(String relPath)：通过相对路径获取到某节点
- Node Node.addNode(String node)：为Node对象添加子节点
- Void Node.remove()：删除节点对象
- Property Node.getProperty(String relPath)：通过相对路径获取属性对象
- Void Node.setProperty(String name, String value)：为Node对象设置属性值
- String Property.getString()：获取属性对象的值
- Value Property.getValue()：获取对属性对象值的原类型封装对象
- String Value.getString()：获取属性值封装对象的实际数据值
- Item Session.getItem(String abspath)：通过绝对路径直接定位到某节点对象
- Void Session.save()：持久化Session对象
- Node Session.getNodeByUUID(String uuid)：通过全局唯一定位符直接定位到有唯一定位符的节点对象





## 为什么要用使用JCR引擎

> 究竟该使用JCR引擎，还是直接使用关系型数据库？



- JCR适合数据浏览（Navigation）和版本化信息查询（Versioning），而RDBMS适合复杂联合查询
- 关系型SQL很强大，但在需要查询整个树状数据时，则需要使用其它模型，另外关系型数据对于版本控制以及访问控制的支持也不好





TODO





## 如何使用？

#### 仓库配置

```xml
<!DOCTYPE Repository
	  PUBLIC "-//The Apache Software Foundation//DTD Jackrabbit 1.5//EN"
	  "http://jackrabbit.apache.org/dtd/repository-1.5.dtd">
<Repository>
  <FileSystem .../>
  <Security .../>
  <Workspaces .../>
  <Workspace .../>
  <Versioning .../>
  <SearchIndex .../>  <!-- optional -->
  <Cluster .../>	  <!-- optional, available since 1.2 -->
  <DataStore .../>	  <!-- optional, available since 1.4 -->
</Repository>
```

- [FileSystem](https://jackrabbit.apache.org/jcr/jackrabbit-configuration.html#file-system-configuration): 存储库用来存储注册名称空间和节点类型等内容的虚拟文件系统
- [Security](https://jackrabbit.apache.org/jcr/jackrabbit-configuration.html#security-configuration): 认证授权配置
- [Workspaces](https://jackrabbit.apache.org/jcr/jackrabbit-configuration.html#workspace-configuration): 配置工作空间的管理位置和方式
- [Workspace](https://jackrabbit.apache.org/jcr/jackrabbit-configuration.html#workspace-configuration): 默认工作区配置模板
- [Versioning](https://jackrabbit.apache.org/jcr/jackrabbit-configuration.html#versioning-configuration): 存储库范围的版本存储的配置
- [SearchIndex](https://jackrabbit.apache.org/jcr/jackrabbit-configuration.html#search-configuration): 覆盖存储库范围的搜索索引的配置 /jcr:system 内容树
- [Cluster](https://jackrabbit.apache.org/jcr/jackrabbit-configuration.html#cluster-configuration): 集群配置
- [DataStore](https://jackrabbit.apache.org/jcr/jackrabbit-configuration.html#data-store-configuration): 数据存储配置



#### 配置中的变量

- `${rep.home}`: Repository home directory.
- `${wsp.name}`: Workspace name. Only available in workspace configuration.
- `${wsp.home}`: Workspace home directory. Only available in workspace configuration.
