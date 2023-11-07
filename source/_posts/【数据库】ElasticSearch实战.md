---
title: ElasticSearch实战
date: 2023-10-31 01:04:01
tags: [ElasticSearch]
categories: 后端/架构
---



如今我们无处不在的使用搜索，这是件好事，因为搜索能帮你从容不迫地完成手头的工作。

无论是在线购物，还是访问博客，你都不会心甘情愿地将整个站点翻个底朝天，而是更希望有个搜索框及时的出现，帮助你发现你真心想要的。



<!--more-->



## 第一章 ElasticSearch介绍

### 1.1 用ElasticSearch解决搜索问题

#### 1.1.1 提供快速查询

基于倒排索引

<img src="http://img.boomclap.cn/uPic/202310/16986865712928fbBQ4.png" alt="image-20231031012251125" style="zoom:66%;" />

#### 1.1.2 确保结果的相关性

根据相关性得分（relevancy score）排序

默认情况下，计算相关性得分的算法是TF-IDF（term frequency-inverse document frequency，词频-逆文档词频）

- 词频——所查找的单词在文档中所出现的次数越多，得分越高。
- 逆文档词频——如果某个单词在所有文档中比较少见，那么该词的权重越高，得分也会越高。

#### 1.1.3 超越精确匹配

