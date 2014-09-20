---
layout: post
title: "Assembly 包含多个段的程序"
description: "assembly muti segment"
category: Assembly
tags: [Assembly]
---
{% include JB/setup %}

###将数据、代码、栈放入不同的段
dw是定义字符型数据

一个段的容量不能大于64KB，是8086模式的限制，并不是所有的处理器都这样

```
assume cs:code,ds:data,ss:stack

data segment
	dw 0123h,0456h
data ends

stack segment
	dw 0,0,0,0,0
stack ends

code segment

	start:  mov ax,stack
			mov ss,ax
			...
			
			mov ax,4c00h
			int 21h
code ends

end start			
```
data是数据段

stack是栈段

code是代码段

这些名字是可以自定义的

start是标号，end start是标志程序从start标号处开始执行

标号名称也可自定义