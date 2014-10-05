---
layout: post
title: "Assembly 综合研究"
description: "assembly comprehensive"
category: Assembly
tags: [Assembly]
---
{% include JB/setup %}

###以下均在TC 2.0上实验
安装Turbo C 2.0，将tc.exe单独拷贝到另一个目录，之后编译连接，根据错误信息拷贝LIB文件到另一目录

C语言真正的入口不是main，main只是那个入口函数的一个call

C语言的函数对应汇编的子程序

全局变量放在数据段，局部变量放在当前栈空间

AX寄存器贯穿于函数内外，AX是C程序中用来存放函数返回值的地方

###不用main函数编程
f.c 源程序：

```
f()
{
	*(char far *)(0xb8000000+160*20+80) = 'a';
	*(char far *)(0xb8000000+160*20+81) = '2';
}
```
连接时报错：Undefined symbol '_main' in module C0S (与c0s.OBJ文件相关)

利用汇编语言的link.exe对f.c进行连接，使用debug查看，与之前的main()程序做对比，发现缺少开头（开头工作）和结尾（善后工作）的汇编指令

---
tc.exe把c0s.obj和用户.obj文件一同进行连接，生成.exe文件，过程如下：

1. c0s.obj 里的程序调用main函数，从此用户程序开始运行（CALL）
2. 用户程序从main函数返回到c0s.obj的程序中（RET）
3. c0s.obj的程序接着运行，进行相关的资源释放，环境恢复等工作
4. c0s.obj的程序调用DOS的int 21h例程的4ch号功能，程序返回

这个规定不是在编译时保证的(tc.exe对f.c的编译是可以通过的)，也不是连接的时候保证的(虽然tc.exe文件对f.obj文件不能连接成.exe，但link.exe却可以)，而是如下机制保证的：

首先，C开发系统提供了用户写的应用程序正确运行所必须的初始化和程序返回等相关程序，这些程序存放在相关的.obj文件(比如,c0s.obj)中

其次，需要将这些文件和用户.obj文件一起进行连接，才能生成可正确运行的.exe文件

第三，连接在用户.obj文件前面的由C语言开发系统提供的.obj文件里的程序要对main函数进行调用

基于这种机制，只要改写c0s.obj让他调用其他函数，编程时就可以不写main函数了：

用汇编语言编写一个c0s.asm:

```
assume cs:code

data segment
	db 128 dup (0)
data ends

code segment
	
	start:mov ax,data
			  mov ds,ax
			  mov ss,ax
			  mov sp,128
			  
			  call s
			  
			  mov ax,4c00h
			  int 21h
			  
		s:
		
code ends

end start
```
用masm.exe对c0s.asm进行编译，生成c0s.obj，并替换tc2.0提供的c0s.obj

注意，完成测试后，需要把tc2.0的c0s.obj恢复

###函数如何接收不定数量的参数
栈传递参数，依次压栈