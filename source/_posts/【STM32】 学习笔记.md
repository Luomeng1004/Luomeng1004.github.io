---
title: STM32 学习笔记（STM32F103C8T6）
date: 2024-08-04 10:48:39
tags: 成长之路
categories: 技术
---

## STM32 学习笔记（STM32F103C8T6）

### 一、环境搭建

https://blog.csdn.net/wv112406/article/details/131626594



<!--more-->



#### ST-LINK V2

*st_nucleo_f103rb.cfg 内容改为如下：因为使用StLink-V2版本*

```
source [find interface/stlink-v2.cfg]
transport select hla_swd
source [find target/stm32f1x.cfg]
```



stm32f103c8_blue_pill.cfg 配置修改如下：

```
set FLASH_SIZE 0x20000
source [find interface/stlink-v2.cfg]
transport select hla_swd
source [find target/stm32f1x.cfg]
```



LED 闪烁代码：

```c
HAL_GPIO_WritePin(GPIOB, GPIO_PIN_0, GPIO_PIN_SET);
HAL_Delay(1000);
HAL_GPIO_WritePin(GPIOB, GPIO_PIN_0, GPIO_PIN_RESET);
HAL_Delay(1000);


HAL_GPIO_WritePin(GPIOB, GPIO_PIN_1, GPIO_PIN_SET);
HAL_Delay(1000);
HAL_GPIO_WritePin(GPIOB, GPIO_PIN_1, GPIO_PIN_RESET);
HAL_Delay(1000);


// 闪烁
HAL_GPIO_WritePin(GPIOB, GPIO_PIN_0, GPIO_PIN_SET);
HAL_Delay(1000);
HAL_GPIO_WritePin(GPIOB, GPIO_PIN_0, GPIO_PIN_RESET);
HAL_Delay(1000);



```





```
which arm-none-eabi-gdb

/opt/homebrew/bin/arm-none-eabi-gdb
```



![在这里插入图片描述](http://img.boomclap.cn/uPic/202407/1722354537041naXXqg.jpeg)






![在这里插入图片描述](http://img.boomclap.cn/uPic/202407/1721844659745tDIGxx.jpeg)





### 二、基础知识点

#### 2.1 HAL 库相关

##### 2.1.1工程模板解读

| 文件                                       | 描述                                                         |
| ------------------------------------------ | ------------------------------------------------------------ |
| **stm32f1xx_hal.c**                        | 包含 HAL 通用 API（比如 HAL_Iint, HAL_DeInit, HAL_Delay等）。 |
| **stm32f1xx_hal.h**                        | HAL 的头文件，它应该被客户代码所包含。                       |
| **stm32f1xx_hal_conf.h**                   | HAL 的配置文件，主要用来选择使能何种外设以及一些时钟相关参数设置。其本身应该被客户代码所包含。 |
| **stm32f1xx_hal_def.h**                    | 包含 HAL 的通用数据类型定义和宏定义                          |
| stm32f1xx_it.c<br />/stm32f1xx_it.h        | 主要是一些中断服务函数的申明。                               |
| stm32f1xx.h                                | 头文件 stm32f1xx.h 内容看似非常少，却非常重要，它是所有 stm32f1 系列的顶层头文件。 |
| stm32f103xx.h                              | 是 stm32f103 系列芯片通用的片上外设访问层头文件，主要是一些结构体和宏定义标识符。<br />这个文件的主要作用是寄存器定义声明以及封装内存操作。 |
| system_stm32f1xx.c<br />system_stm32f1xx.h | 主要是声明和定义了系统初始化函数 SystemInit 以及系统时钟更新函数 SystemCoreClockUpdate。 |
| stm32f1xx_hal_msp.c                        | 进行 MCU 级别硬件初始化设置                                  |
| startup_stm32f103xe.s                      | 启动文件的作用主要是进行堆栈的初始化                         |









### 附录

#### 为什么要有 16 进制？











































