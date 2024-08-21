---
title: STM32 学习笔记-代码库（HAL版本）
date: 2024-08-21 23:12:39
tags: 成长之路
categories: 技术
---



收集总结代码库



<!--more-->



### LED

led.h

```c
//
// Created by Titan on 24-8-17.
//

#ifndef LED_H
#define LED_H

#include "stm32f1xx_hal.h" //HAL库文件声明
#include "main.h" //IO定义与初始化函数在main.c文件中，必须引用

void LED_1(uint8_t a);//LED1独立控制函数（0为熄灭，其他值为点亮）
void LED_2(uint8_t a);//LED2独立控制函数（0为熄灭，其他值为点亮）
void LED_ALL(uint8_t a);//LED1~4整组操作函数（低4位的1/0状态对应4个LED亮灭，最低位对应LED1）
void LED_1_Contrary(void);//LED1状态取反
void LED_2_Contrary(void);//LED2状态取反

#endif //LED_H
```

led.c

```c
//
// Created by Titan on 24-8-17.
//

#include "led.h"

void LED_1(uint8_t a)//LED1独立控制函数（0为熄灭，其他值为点亮）
{
	if(a)HAL_GPIO_WritePin(GPIOB,LED1_Pin,GPIO_PIN_SET);
	else HAL_GPIO_WritePin(GPIOB,LED1_Pin,GPIO_PIN_RESET);
}
void LED_2(uint8_t a)//LED2独立控制函数（0为熄灭，其他值为点亮）
{
	if(a)HAL_GPIO_WritePin(GPIOB,LED2_Pin,GPIO_PIN_SET);
	else HAL_GPIO_WritePin(GPIOB,LED2_Pin,GPIO_PIN_RESET);
}
void LED_ALL(uint8_t a)//LED1~2整组操作函数（低2位的1/0状态对应2个LED亮灭，最低位对应LED1）
{
	if(a&0x01)HAL_GPIO_WritePin(GPIOB,LED1_Pin,GPIO_PIN_SET);
	else HAL_GPIO_WritePin(GPIOB,LED1_Pin,GPIO_PIN_RESET);
	if(a&0x02)HAL_GPIO_WritePin(GPIOB,LED2_Pin,GPIO_PIN_SET);
	else HAL_GPIO_WritePin(GPIOB,LED2_Pin,GPIO_PIN_RESET);
}
void LED_1_Contrary(void){
	HAL_GPIO_WritePin(GPIOB,LED1_Pin,1-HAL_GPIO_ReadPin(GPIOB,LED1_Pin));
}
void LED_2_Contrary(void){
	HAL_GPIO_WritePin(GPIOB,LED2_Pin,1-HAL_GPIO_ReadPin(GPIOB,LED2_Pin));
}
```

main.c

```c
/* USER CODE BEGIN Includes */
#include "../../icode/led/led.h"
/* USER CODE END Includes */

while (1)
  {

    LED_1(1);
    HAL_Delay(100);
    LED_1(0);
    HAL_Delay(100);

    /* USER CODE END WHILE */

    /* USER CODE BEGIN 3 */
  }
```











































