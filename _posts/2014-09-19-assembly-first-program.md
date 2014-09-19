---
layout: post
title: "Assembly 第一个程序"
description: "assembly first program"
category: Assembly
tags: [Assembly]
---
{% include JB/setup %}

###一个源程序从写出到执行的过程
1.编写汇编源程序

2.对源程序进行编译连接

&nbsp;&nbsp;&nbsp;&nbsp;使用汇编语言编译程序对源文件中的源程序进行编译，产生目标文件；再用连接程序对目标文件进行连接，生成可在操作系统中直接运行的可执行文件

3.执行可执行文件中的程序

###<span id="meta">源程序</span>
```
assume cs:codesg

codesg segment

		mov ax,0123H
		mov bx,0456H
		add ax,bx
		add ax,ax
		
		mov ax,4c00H
		int 21H
		
codesg ends

end

```
####1.伪指令
在汇编语言源程序中，包含2种指令：汇编指令、伪指令。

汇编指令是有对应的`机器码`的指令，可以被编译为机器指令，最终CPU所执行

伪指令没有对应的机器指令，最终不被CPU所执行

伪指令是由`编译器`来执行的指令

------
segment和ends是成对使用的伪指令，功能是定义一个段

end 是一个汇编程序的结束标记，编译器在编译汇编程序中，当碰到伪指令end就结束对源程序的编译

assume 含义为“假设”，（不重要），用assume 将有特定用途的段和相关的段寄存器关联

####2.源程序中的"程序"
将源程序中最终由计算机执行、处理的指令或数据，成为程序。程序最先以汇编指令的形式存在源程序中，经编译、连接后转变为机器码，存储在可执行文件中。


####3.标号
比如“codesg”，一个标号指代了一个地址。比如codesg在segment前面，作为一个段的名称，这个段的名称最终将被编译、连接程序处理为一个段的段地址

####4.程序的结构
[如上代码](#meta)

####5.程序返回
程序返回：一个程序结束后，将CPU的控制权交还给使它得以运行的程序

```
	mov ax,4c00H
	int 21H
```
这两条指令所实现的功能就是程序返回

####6.语法错误和逻辑错误
程序在编译时，被编译器发现的错误是语法错误

运行时发生的错误是逻辑错误，不容易被发现

###编辑源程序
[如上代码](#meta)
保存为1.asm
###编译
win32下

进入dos方式，进入c:\masm目录，运行masm.exe

x86_64 or unix-like
使用dosbox

或者配置环境变量（自行Google）

###连接
c:\masm\link.exe

###简化编译连接
masm c:\1`;`

link c:\1`;`

###谁将可执行文件中的程序装载进入内存并使它执行？
1.在dos中直接执行1.exe时，是正在运行的command，将1.exe中的程序加载入内存

2.command 设置CPU的CS:IP 指向程序的第一条指令（即程序的入口），从而使程序得以运行

3.程序运行结束后，返回到command中，CPU继续运行command

###程序执行过程的追踪
`debug` 1.exe

------
DOS系统中.exe文件中的程序的加载过程：

![](/public/image/dos-loading-exe.jpg)