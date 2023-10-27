---
title: 让 Spring Session 不在执行 Config 命令
date: 2022-06-13 14:40:24
tags: [问题解决]
categories: 后端/架构
---

## 让 Spring Session 不再执行 Config 命令

在集成 Spring Session 做session共享的时候，有时候我们服务器是限制使用CONFIG命令的，这就导致在项目启动的时候会没有授权的错误，通过以下方法可以屏蔽 Spring Session 的CONFIG命令，让程序可以正常运行。



```java
@Configuration
public class RedisConfig {

    @Bean
    public ConfigureRedisAction configureRedisAction() {
        return ConfigureRedisAction.NO_OP;
    }

}
```

 代码是基于 Spring Boot 的，其他请自行翻译。
