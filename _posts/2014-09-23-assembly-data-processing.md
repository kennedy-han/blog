---
layout: post
title: "Assembly 数据处理两个基本问题"
description: "assembly flex focus ram"
category: Assembly
tags: [Assembly]
---
{% include JB/setup %}

###声明约定
reg表示一个寄存器，sreg表示一个段寄存器

###bx、si、di 和 bp
在8086CPU中，只有这4个寄存器可以用在[...]内存单元寻址

可以单独出现，或只能以4种组合出现

[bx+si]

[bx+di]

[bp+si]

[bp+di]

只要在[...]中使用寄存器`bp`，而指令中没有显性的给出段地址，段地址默认在`ss`中

###机器指令处理的数据在什么地方
CPU内部、内存、端口

###寻址方式
![](/blog/public/image/addressing.png)

###指令要处理的数据有多长
8086CPU的指令可以处理两种尺寸的数据,byte和word

使用ax等16位寄存器进行的是word操作

使用al等8位寄存器进行的是byte操作

####在没有寄存器名存在的情况
字操作

```
mov word ptr ds:[0],1
inc word ptr [bx]
add word ptr [bx],2
```
字节操作

```
mov byte ptr ds:[0],1
inc byte ptr [bx]
add byte ptr [bx],2
```

push指令只进行`字`操作

---
###div 指令
div是除法指令

1.除数：有8位和16位两种，在一个reg或内存单元中

2.被除数：默认放在AX或DX或AX中，如果除数为8位，被除数则位16位
默认在AX中存放；如果除数为16位，被除数则为32位，在DX和AX中存放，DX存高16位，AX存放低16位

3.结果：如果除数为8位，则AL存储除法操作的商，AH存储除法操作的余数

格式如下：

```
div reg
div 内存单元

div byte ptr ds:[0]
含义：(al)=(ax)/((ds)*16+0)的商
	 (hl)=(ax)/((ds)*16+0)的余数
```

###伪指令dd
dd是用来定义 dword(double word，双字)型数据的

`dd 1`

数据为 00000001H 占2个字（4字节）

---
###伪指令dup
是一个操作符，它是和db dw dd等数据定义伪指令配合使用的，用来进行数据的重复

```
db 3 dup (0)	;定义了3个字节，相当于 db 0,0,0
db 3 dup (0,1,2)	;定义了9个字节，相当于 db 0,1,2,0,1,2,0,1,2
```