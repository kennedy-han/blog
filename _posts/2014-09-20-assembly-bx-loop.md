---
layout: post
title: "Assembly [bx] loop"
description: "assembly [bx] loop"
category: Assembly
tags: [Assembly]
---
{% include JB/setup %}

###[bx]
```
mov ax,[bx]
```
AX=((ds)*16 + (bx))

###loop 指令
CPU执行loop指令需进行2步操作：

1.(cx)=(cx)-1

2.判断cx中的值，不为零则转至标号处执行程序，如果为零则向下执行

------
编程，用加法计算 123 * 236 结果存在AX中

```
assume cs:code

code segment
	mov ax,0
	mov cx,123	;将236加123次
  s:add ax,236
  	loop s
  	
  	mov ax,4c00h
  	int 21h
 
code ends

end 
```

在源程序中，数据不能以字母开头，所以要在前面加`0`

如：0A000H、0A001H

###在debug中跟踪用loop指令实现的循环程序
使用g 0016

其中0016是指令的偏移地址（IP）

功能相当于运行到0016

###debug和汇编编译器masm对指令的不同处理
```
mov ax,[0]
```
debug中可以理解为 ax = ds:0

汇编编译器编译为 ax = 0

所以要在前面加上段寄存器

```
mov ax,ds:[0]
```

###段前缀
可以在访问内存单元指令中现实的给出内存单元的段地址所在的段寄存器

如：mov ax,ds:[bx]

mov ax,cs:[bx]

mov ax,ss:[bx]

mov ax,ss:[0]

###一段安全的空间
在8086模式中，随意向一段内存空间写入内容是很危险的

在纯DOS方式（实模式）下，可以不理会DOS，直接用汇编语言去操作真实地硬件，因为运行在CPU实模式下的DOS，没有能力对硬件系统进行全面、严格的管理

在windows unix这些运行于CPU保护模式下的OS中，不理会OS直接操作硬件是不可能的，硬件已被这些OS利用CPU保护模式所提供的功能全面而严格的管理了

`0:200~0:2ff` 这256个字节的空间一般不会被DOS和其他合法程序使用

------
第五章源码在[github](https://github.com/kennedy-han/myAsmCode/tree/master/chapter5_bx_loop)