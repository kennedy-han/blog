---
layout: post
title: "Assembly 转移指令的原理"
description: "assembly transfer"
category: Assembly
tags: [Assembly]
---
{% include JB/setup %}

###可以修改IP，或同时修改CS和IP的指令统称为转移指令

8086CPU的转移行为有以下几类：

* 只修改IP时，称为段内转移，比如：jmp ax
* 同时修改CS和IP时，称为段间转移，比如：jmp 1000:0

由于转移指令对IP修改的范围不同，段内转移又分为：短转移和近转移

* <span id="short">短转移</span>IP的修改范围为-128~127
* <span id="long">近转移</span>IP的修改范围为-32768~32767

8086CPU的转移指令分为以下几类：

* 无条件转移指令（如:jmp）
* 条件转移指令（如：je）
* 循环指令 （如:loop）
* 过程
* 中断	（如：int）

###操作符offset
用来取得标号的偏移地址

```
start:mov ax,offset start	;相当于mov ax,0
	s:mov ax,offset s	;相当于mov ax,3
```
###jmp指令
为无条件转移指令，可以只修改IP，也可以同时修改CS和IP

jmp指令要给出两种信息：

1. 转移的目的地址
2. 转移的距离（段间转移、段内短转移、段内近转移）

###根据位移进行转移的jmp指令
jmp short 标号（转到标号处执行指令）

这种格式的jmp指令实现的是 [段内短转移](#short)

向前转移时可以最多越过128个字节，向后转移可以最多越过127个字节

CPU在执行jmp指令的时候并不需要转移的目的地址，在编译时，算出CPU要转移的位移

jmp short 标号的功能为：（IP）=(IP)+8位位移

jmp near ptr 标号的功能为：（IP）=(IP)+16位位移

###转移的目的地址在指令中的jmp指令
```
jmp far ptr 标号
```
实现的是段间转移，又称为远转移

机器码包含转移的目的地址

###转移地址在寄存器中的jmp指令(段内近转移)
格式：jmp 16位reg

功能：（IP）=（16位reg）

###转移地址在内存中的jmp指令
1. jmp word ptr 内存单元地址（段内转移）
	
	```
	mov ax,0123h
	mov ds:[0],ax
	jmp word ptr ds:[0]
	```
	执行后（IP）=0123H
	
2. jmp dword ptr 内存单元地址（段间转移）
	
	```
	mov ax,0123h
	mov ds:[0],ax
	mov word ptr ds:[2],[0]
	jmp dword ptr ds:[0]
	```
	执行后(CS)=0,（IP）=0123H
	
	CS=内存单元地址+2
	
	IP=内存单元地址
	
###jcxz指令
为有条件转移指令，所有有条件的转移指令都是短转移（-128~127）

格式：jcxz 标号 （如果cx等于0，则转移到标号执行）

可以理解为：if( (cx) == 0 ) jmp short 标号;

###loop指令
loop指令是循环指令，所有的循环指令都是短转移，在对应的机器码中包含转移的位移

格式：loop 标号 

操作：(cx)=(cx)-1
	  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;如果(cx)不等于0，（IP）=（IP）+8位位移

###根据位移转移的意义
jmp short 标号

jmp near ptr 标号

jcxz 标号

loop 标号

机器码都是保存的位移，装在内存中的不同位置都可正确执行（好比相对路径）

###编译器对转移位移的超界的检测
如果在源程序中出现了转移范围超界的问题，在编译的时候，编译器将报错

###分析一个奇怪的程序
源代码戳这里 --> [e8.asm](https://github.com/kennedy-han/myAsmCode/blob/master/chapter9_transfer/e8.asm)

运行前思考，这个程序可以正确返回吗？

###小结
|    转移指令   |  类型       |   范围   |  机器码  |修改CS或IP|
|--------------------------------------------------------|
|jmp short ptr|段内短转移     |-128~127    |转移的位移| IP  |
|jmp near ptr |段内近转移     |-32768~32767|转移的位移| IP  |
|jmp far ptr  |段间转移(远转移)| unlimited |目的地址  |CS和IP|

####所有`有条件`的转移指令都是`短转移`（-128~127）