---
layout: post
title: "Assembly 直接定址表"
description: "assembly direct addr"
category: Assembly
tags: [Assembly]
---
{% include JB/setup %}

有效的合理的组织数据的编程技巧

###描述了单元长度的标号（数据标号）
```
assume cs:code
	code segment
		a db 1,2,3,4,5,6,7,8
		b dw 0
		
  start: mov si,0
  		 mov cx,8
  	  s: mov al,a[si]	  
  		mov ah,0
  		add b,ax
  		inc si
  		loop s
  		
  		mov ax,4c00h
  		int 21h	  
	code ends
end start
```
在code段中，标号a,b后面，没有“：”，它们是同事描述内存地址和单元长度的标号

这种标号不但表示内存单元的地址，还表示了内存单元的长度（此单元是一个字单元还是字节单元）

###在其他段中使用数据标号
注意，后面加有“：”的地址标号，只能在代码段中使用，不能在其他段中使用

其他段使用数据标号，需要使用伪指令assume关联，这种联系是编译器需要的：

```
assume cs:code,ds:data

data segment
	a db 1,2,3,4,5,6,7,8
	b dw 0
data ends
```

可以将标号当做数据来定义，此时，编译器将标号所表示的地址当做数据的值：

```
data segment
	a db 1,2,3,4,5,6,7,8
	b dw 0
	c dw a,b	;相当于 c dw offset a,offset b
	d dd a,b	;相当于 d dd offset a,seg a,offset b,seg b
data ends
```
数据标号 c 处存储的两个字型数据为标号 a、b 的偏移地址

seg操作符功能为取得某一标号的段地址

###直接定址表
```
start:jmp short begin
	  table dw func0,func1,func2	;字符串偏移地址表
	  
begin:mov bl,0	;bl为传入参数，0为调用0号功能，1为调用2号功能
	  mov bh,0
	  add bx,bx	;bl+bl = 0 下面call寻址到table[0]
	  call word ptr table[bx]
	  
	  mov ax,4c00h
	  int 21h
	  
func0:......ret
func1:......ret
func2:......ret
```
注意，table使用dw定义，即一个字（2字节）

bl为0时，bx+bx=0 用table[0]寻址找到func0子程序

bl为1时，bx+bx=2 用table[2]寻址找到func1子程序

bl为2时，bx+bx=4 用table[4]寻址找到func2子程序

根据功能号查找地址表的方法，程序的结构清晰，易于扩充，如果新加入一个功能，只需要在地址表中加入它的入口地址就可以了