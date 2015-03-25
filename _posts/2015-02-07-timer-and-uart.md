---
layout: post
title: "系统时钟和UART"
description: "timer and UART"
category: embedded
tags: [embedded]
---
{% include JB/setup %}

###系统时钟和UART实验

####对比PC和开发板的硬件配置：
#####PC：

CPU 1G，2G...

内存：133Mhz...

#####开发板：
2440CPU:400MHz --> FCLK

SDRAM: 100M ~ 130M --> HCLK

UART: 50MHz --> PCLK

------

####怎么设置:FCLK、HCLK、PCLK
1. 晶振 --> PLL --> 400MHz
2. 400MHz 分频

####PC如何用串口：
1. 确定用哪个串口      --> 初始化

2. 波特率、流控         --> 把引脚设置为UART、波特率、流控

3. 打开

4. 发/收

