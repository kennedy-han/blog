---
layout: post
title: "储存管理器实验"
description: "Memory Controller"
category: embedded
tags: [embedded]
---
{% include JB/setup %}

###存储管理器实验

####程序启动过程
1. 一上电，Nand前4K --> CPU片内RAM
2. 关看门狗，初始化存储器、管理器
3. 把代码拷到SDRAM，继续执行（在SDRAM执行）

head.S文件：

```
@*************************************************************************
@ File：head.S
@ 功能：设置SDRAM，将程序复制到SDRAM，然后跳到SDRAM继续执行
@*************************************************************************      

.equ        MEM_CTL_BASE,       0x48000000
.equ        SDRAM_BASE,         0x30000000

.text
.global _start
_start:
    bl  disable_watch_dog               @ 关闭WATCHDOG，否则CPU会不断重启
    bl  memsetup                        @ 设置存储控制器
    bl  copy_steppingstone_to_sdram     @ 复制代码到SDRAM中
    ldr pc, =on_sdram                   @ 跳到SDRAM中继续执行
on_sdram:
    ldr sp, =0x34000000                 @ 设置堆栈
    bl  main
halt_loop:
    b   halt_loop

disable_watch_dog:
    @ 往WATCHDOG寄存器写0即可
    mov r1,     #0x53000000
    mov r2,     #0x0
    str r2,     [r1]
    mov pc,     lr      @ 返回

copy_steppingstone_to_sdram:
    @ 将Steppingstone的4K数据全部复制到SDRAM中去
    @ Steppingstone起始地址为0x00000000，SDRAM中起始地址为0x30000000
   
    mov r1, #0
    ldr r2, =SDRAM_BASE
    mov r3, #4*1024
1: 
    ldr r4, [r1],#4     @ 从Steppingstone读取4字节的数据，并让源地址加4
    str r4, [r2],#4     @ 将此4字节的数据复制到SDRAM中，并让目地地址加4
    cmp r1, r3          @ 判断是否完成：源地址等于Steppingstone的未地址？
    bne 1b              @ 若没有复制完，继续
    mov pc,     lr      @ 返回

memsetup:
    @ 设置存储控制器以便使用SDRAM等外设

    mov r1,     #MEM_CTL_BASE       @ 存储控制器的13个寄存器的开始地址
    adrl    r2, mem_cfg_val         @ 这13个值的起始存储地址
    add r3,     r1, #52             @ 13*4 = 52
1: 
    ldr r4,     [r2], #4            @ 读取设置值，并让r2加4
    str r4,     [r1], #4            @ 将此值写入寄存器，并让r1加4
    cmp r1,     r3                  @ 判断是否设置完所有13个寄存器
    bne 1b                          @ 若没有写成，继续
    mov pc,     lr                  @ 返回


.align 4
mem_cfg_val:
    @ 存储控制器13个寄存器的设置值
    .long   0x22011110      @ BWSCON
    .long   0x00000700      @ BANKCON0
    .long   0x00000700      @ BANKCON1
    .long   0x00000700      @ BANKCON2
    .long   0x00000700      @ BANKCON3 
    .long   0x00000700      @ BANKCON4
    .long   0x00000700      @ BANKCON5
    .long   0x00018005      @ BANKCON6
    .long   0x00018005      @ BANKCON7
    .long   0x008C07A3      @ REFRESH
    .long   0x000000B1      @ BANKSIZE
    .long   0x00000030      @ MRSRB6
    .long   0x00000030      @ MRSRB7
```

leds.c文件与之前的一样，轮流点亮LED

Makefile:

```
sdram.bin : head.S  leds.c
     arm-linux-gcc  -c -o head.o head.S
     arm-linux-gcc -c -o leds.o leds.c
     arm-linux-ld -Ttext 0x30000000 head.o leds.o -o sdram_elf
     arm-linux-objcopy -O binary -S sdram_elf sdram.bin
     arm-linux-objdump -D -m arm  sdram_elf > sdram.dis
clean:
     rm -f   sdram.dis sdram.bin sdram_elf *.o
```

指定代码段从0x30000000开始

之后编译烧写sdram.bin到Nand，切换Nand启动，看到LED闪烁速度比较慢，原因是SDRAM的速度比SRAM要慢

####汇编指令备注：
ldr r4,     [r2], #4 @把r2这个地址中的值存放到r4，并让r2加4

pc = 当前地址 + 8

ldr     pc, [pc, #144]

[pc, #144]相当于[0xc + 8 + #144 = 12 + 8 + 144 = 164 = 0xA4]

执行ldr     pc, [pc, #144]后，pc=30000010 (300000a4中的值)就跳转到SDRAM中

使用BL与mov pc,lr实现调用子程序:

```
bl  disable_watch_dog
bl  memsetup
disable_watch_dog:
    @ 往WATCHDOG寄存器写0即可
    mov r1,     #0x53000000
    mov r2,     #0x0
    str r2,     [r1]
    mov pc,     lr      @ 返回，执行bl  memsetup指令
```