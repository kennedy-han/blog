---
layout: post
title: "Nand Flash控制器"
description: "Nand flash exp"
category: embedded
tags: [embedded]
---
{% include JB/setup %}

###NAND FLASH控制器

1. SDRAM、DM9000的Addr线 连接到2440 Addr

2. Nand 没有Addr 寻址方式不同


####CPU统一编址：
1. 片内内存
2. SDRAM
3. 网卡
4. 寄存器

####Nand的编址：
一页有2048字节+64字节OOB

64页组成一块

####访问内存：
1. 发出地址信号（R/W）
2. 传输DATA

####访问Nand：
1. 发送命令（R、W、E）
2. 发送地址
3. 数据

####从硬件上访问Nand：
1. 命令 CLE, DATABUS
2. Addr ALE, DATABUS
3. 传输数据R、W

####2440访问Nand：
1. NFCMMD
2. NFADDR
3. NFDATA
4. 状态：NFSTAT

从Nand启动，一上电就会把Nand前4K内容，复制到片内内存

C函数参数与汇编的r0,r1,r2对应

注意：寄存器是unsigned char 8位