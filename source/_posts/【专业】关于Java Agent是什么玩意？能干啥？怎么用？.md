---
title: 关于Java Agent是什么玩意？能干啥？怎么用？
date: 2023-12-09 17:22:01
tags: [Java]
categories: 专业
---



公司打算搭建一个分布式链路追踪系统，经过一段时间的调研和技术选型，最终确定以Skywalking为主搭建分布式链路追踪系统，用于监测公司内各个应用的运行情况。

Skywalking是一个开源的可观测平台，其采用Java Agent（字节码技术，也称为Java代理、Java探针），主要用于收集、分析、聚合和可视化来自应用服务的数据，实现对应用的观察和监控。

<!--more-->
