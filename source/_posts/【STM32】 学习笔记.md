---
title: STM32 学习笔记（STM32F103C8T6）
date: 2024-08-04 10:48:39
tags: 成长之路
categories: 技术
---



### 一、环境搭建

https://blog.csdn.net/wv112406/article/details/131626594



<!--more-->



#### 1.1 ST-LINK V2

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



时钟树设置：


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

1. **简化二进制表示**：计算机内部所有信息都是以二进制（Binary）形式存储的，即只包含0和1两种状态。然而，二进制数在表示和阅读时非常冗长且容易出错。例如，一个简单的十进制数255，在二进制中需要8位来表示（11111111）。相比之下，使用16进制表示这个数只需要2个字符（FF），这使得在需要时能够快速地在二进制和更易于人类阅读的表示之间转换。
2. **便于内存和地址的表示**：在计算机系统中，内存地址和数据大小经常以字节为单位进行描述。由于一个字节由8位二进制数组成，使用16进制可以很方便地表示这些值。例如，一个4字节的整数在内存中可能占用从地址0x1000到0x1003的连续空间，这种表示方式比使用二进制或十进制更直观。
3. **易于转换**：16进制与二进制之间的转换非常直接，因为每一位16进制数都可以直接对应到4位二进制数（例如，十六进制中的'A'代表二进制的1010）。这种关系使得在需要时能够快速地在这两种表示之间转换，便于调试和分析计算机中的数据。
4. **颜色编码**：在图形和图像处理中，颜色经常以RGB（红、绿、蓝）值的形式表示，每种颜色的强度范围是0到255，这正好是一个字节的范围。因此，使用16进制表示颜色代码（如#FF0000代表红色）变得非常方便和直观。
5. **网络通信**：在网络通信中，数据包的格式和内容通常以16进制形式进行展示和分析，因为这样可以清楚地看到数据的具体结构和内容，有助于网络工程师和开发人员调试和诊断网络问题。

| 二进制 | 十六进制 | 二进制 | 十六进制 |
| ------ | -------- | ------ | -------- |
| 0000   | 0        | 1000   | 8        |
| 0001   | 1        | 1001   | 9        |
| 0010   | 2        | 1010   | A        |
| 0011   | 3        | 1011   | B        |
| 0100   | 4        | 1100   | C        |
| 0101   | 5        | 1101   | D        |
| 0110   | 6        | 1110   | E        |
| 0111   | 7        | 1111   | F        |







































