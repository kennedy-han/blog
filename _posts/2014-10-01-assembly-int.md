---
layout: post
title: "Assembly int指令"
description: "assembly int"
category: Assembly
tags: [Assembly]
---
{% include JB/setup %}

CPU执行int n指令，相当于引发一个n号中断的中断过程，执行过程：

1. 取中断类型码n
2. 标志寄存器入栈，IF=0,TF=0
3. CS、IP入栈
4. (IP)=(n*4)，(CS)=(n\*4+2)

可以在程序中使用int指令调用任何一个中断的中断处理程序

###iret指令
功能为：

```
pop IP
pop CS
popf
```
与int一同使用，从而接着执行应用程序，类似于call与ret

###对int、iret和栈的深入理解
问题：用7ch中断例程完成loop指令的功能

[源代码](https://github.com/kennedy-han/myAsmCode/blob/master/chapter13_int/p13-3.asm)

###BIOS和DOS所提供的中断例程
在系统版的ROM中存放这一套程序称为BIOS（基本输入输出系统），包含：

1. 硬件系统的检测和初始化程序
2. 外部中断和内部中断的中断例程
3. 用于对硬件设备进行I/O操作的中断例程
4. 其他和硬件系统相关的中断例程

DOS的中断例程就是操作系统向程序员提供的编程资源

###BIOS和DOS中断例程的安装过程
1. 开机后，CPU一加电，初始化(CS)=0FFFFH，(IP)=0，自动从FFFF:0单元开始执行程序。FFFF:0有一条跳转指令，CPU执行该指令后，转去执行BIOS中的硬件系统检测和初始化程序
2. 初始化程序将建立BIOS所支持的中断向量，即将BIOS提供的中断例程的入口地址登记在中断向量表中。注意，对于BIOS所提供的中断例程，只需将入口地址登记在中断向量表中即可，因为他们是`固化到ROM中的程序，一直在内存中存在`。
3. 硬件系统检测和初始化完成后，调用int 19h进行操作系统的引导。从此将计算机交由操作系统控制。
4. DOS启动后，除完成其他工作外，还将它所提供的中断例程装入内存，并建立相应地中断向量。

###BIOS中断例程应用
int 10h中断例程是BIOS所提供，包含了多个屏幕输出相关的子程序

BIOS和DOS提供的中断例程，都用`ah`来传递内部子程序的编号

[源代码](https://github.com/kennedy-han/myAsmCode/blob/master/chapter13_int/p13-4.asm)

###DOS中断例程的应用
DOS为程序员提供了许多可以调用的子程序，都包含在int 21h

前面一直使用int 21h中断例程的4ch号子程序，功能为程序返回

[源代码](https://github.com/kennedy-han/myAsmCode/blob/master/chapter13_int/p13-5.asm)