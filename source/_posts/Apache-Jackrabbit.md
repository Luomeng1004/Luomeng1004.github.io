---
title: Apache Jackrabbit
date: 2022-05-20 00:28:41
tags: 数据库
categories: 职业技能
---

## Apache Jackrabbit

### 配置

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
