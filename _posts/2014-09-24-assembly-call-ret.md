---
layout: post
title: "Assembly Call和ret指令"
description: "assembly call ret"
category: Assembly
tags: [Assembly]
---
{% include JB/setup %}

###call和ret指令都是转移指令，他们都修改IP，或同时修改CS和IP。他们经常被共同用来实现子程序的设计

###ret和retf
ret 使用栈中的数据，修改IP的内容，实现近转移

retf使用栈中的数据，修改CS和IP的内容，从而实现远转移

CPU执行ret指令时，相当于: 

pop IP

CPU执行retf指令时，相当于: 

pop IP 

pop CS

###call指令
CPU执行call时，进行2步操作：

1. 将当前的IP或CS和IP压入栈中
2. 转移

call指令不能实现短转移

###依据位移进行转移的call指令
call 标号（将当前的IP压栈后，转到标号处执行指令）

机器码没有转移的目的地址，而是相对于当前IP的转移位移

相当于：

```
push IP
jmp near ptr 标号
```

###转移的目的地址在指令中的call指令
call far ptr 标号（实现的是段间转移，机器码中有目标地址）

相当于：

```
push CS
push IP
jmp far ptr 标号
```

###转移地址在寄存器中的call指令
指令格式：call 16位reg

相当于：

```
push IP
jmp 16位reg
```

###转移地址在内存中的call指令
1. call word ptr 内存单元地址

	相当于：

	```
	push IP
	jmp word ptr 内存单元地址
	```

2. call dword ptr 内存单元地址

	相当于：
	
	```
	push CS
	push IP
	jmp dword ptr 内存单元地址
	```
	
###call和ret的配合使用
写一个具有一定功能的程序段，称为子程序，在需要的时候，用call指令转去执行，call转去执行之前，先将call指令后面的指令的地址存储在栈中，在子程序使用ret指令返回，用栈中数据设置IP的值，从而转到call后面的代码处继续执行

###mul指令
mul是乘法指令，注意两点：

1. 两个相乘的数：要么都是8位，要么都是16位。如果是8位，一个默认放在AL中，另一个放在8位reg或内存字节单元中；如果是16位，一个默认在AX中，另一个放在16位reg或内存单元中

2. 结果：如果是8位乘法，结果默认放在AX中；如果是16位乘法，结果高位默认在DX中存放，低位在AX中放

格式如下：

mul reg

mul 内存单元

例：

mul byte ptr ds:[0]

含义：(ax)=(al) * ( (ds)*16+0)

计算100*10:

```
mov al,100
mov bl,10
mul bl
```
###模块化程序设计
批量参数的传递：

批量数据放到内存中，然后将他们所在内存空间的首地址放在寄存器中，传递给需要的子程序。批量数据的返回结果，也可以用同样的方法

###寄存器冲突的问题
子程序中使用的寄存器很可能和主程序产生冲突

子程序执行前将程序中用到的寄存器中的内容都保存起来，可以用栈来保存寄存器中的内容，子程序返回前再恢复

要注意寄存器入栈和出栈的顺序

###除法溢出
结果的商过大，超出了寄存器所能存储的范围。

```
mov ax,1000H
mov dx,1
mov bx,1
div bx
```

进行的是16位除法，结果的商为11000H  而11000H在AX中存放不下