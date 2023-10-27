---
title: hexo使用手册
date: 2022-05-21 13:17:16
tags: hexo
categories: 
---



### 服务安装

```linux
# 初始化
hexo init [folder]


# 新建一篇文章
hexo new [layout] <title>
  
hexo new page --path about/me "About me"
  
# 生成静态文件
hexo generate

# 发表草稿
hexo publish [layout] <filename>

# 启动服务
hexo server
```



### 服务器安装

```
npm install hexo-server --save

# 网站会在 http://localhost:4000 下启动
hexo server

# 指定端口启动
hexo server -p 5000
```



### 部署

```
hexo deploy
```

