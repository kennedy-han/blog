---
layout: post
title: "Assembly 灵活定位内存地址"
description: "assembly flex focus ram"
category: Assembly
tags: [Assembly]
---
{% include JB/setup %}

###and 和 or指令
1.and指令：逻辑与指令，按位进行与计算

例：

```
mov al,01100011B
and al,00111011B
```
执行后al值为：00100011B

通过该指令可将操作对象的相应位设为0，其他位不变

2.or指令：逻辑或指令，按位进行或计算

例：

```
mov al,01100011B
or	al,00111011B
```
执行后al值为：01111011B

通过该指令可将操作对象的相应位设为1，其他位不变

###关于ASCII码
ASCII编码方案就是一套规则，约定了用什么样的信息来表示现实对象，比如说，在ASCII编码方案中，用61H表示“a”,62H表示“b”

显卡在处理文本信息的时候，是按照ASCII码的规则进行

###以字符形式给出的数据
汇编源程序中用'....'的方式指明数据是以字符的形式给出的，编译器将把它们自动转化为相对的ASCII码

如：

```
data segment
	db 'unIX'
	db 'foRK'
data ends

code segment
	mov al,'a'
	mov bl,'b'
code ends
```
db 'unIX' 相当于 "db 75H,6EH,49H,58H", “u”、“n”、“I”、“X” ASCII码分别是 75H,6EH,49H,58H

###大小写转换问题
|大写|十六进制|   二进制  |小写|十六进制|   二进制  |
|---------------------------------------------|
| A |  41   |01`0`00001| a |   61  |01`1`00001|
| B |  42   |01`0`00010| b |   62  |01`1`00010|
| C |  43   |01`0`00011| c |   63  |01`1`00011|

可以使用十六进制 20H：

将“A”加上20H 得到 “a”

将“a”减去20H 得到 “A”

`推荐`使用二进制 and 和 or：

and al,11011111B;	将al中的ASCII码的第5位设为0，变为大写

or	al,00100000B;	将al中的ASCII码的第5位设为1，变为小写

###[bx+idata]
mov ax,[bx+200]

作用：(AX)=((ds)*16+(bx)+200)

###用[bx+idata]的方式进行数组的处理
C语言：a[i], b[i]

汇编语言：0[bx], 5[bx]

###SI 和DI
si和di 是8086CPU中和bx功能相近的寄存器，si和di不能够分成两个8位寄存器来使用

例：

```
mov bx,0
mov ax,[bx]

mov si,0
mov ax,[si]
```

###[bx+si]和[bx+di]
mov ax,[bx+si]

作用：ax=(ds*16 + bx+si)

也可以写为 mov ax,[bx][si]

###[bx+si+idata]和[bx+di+idata]
mov ax,[bx+si+5]

作用：ax=(ds*16+ bx+si+5)

也可以写成：

```
mov ax,[bx+200+si]
mov ax,[200+bx+si]
mov ax,200[bx][si]
mov ax,[bx].200[si]
mov ax,[bx][si].200
```

###不同的寻址方式的灵活应用
[idata]用一个常量表示偏移地址，可用于直接定位一个内存单元

[bx] 用一个变量表示偏移地址，用于间接定位

[bx+idata]用一个变量和一个常量表示

[bx+si]两个变量

[bx+si+idata] 两个变量一个常量

###程序中经常需要数据的暂存，怎样做更为合理
有些数据是寄存器中的，也可能是内存中的

一般来说，在需要暂存数据的时候，我们都应该使用栈

###小结
* 寻址方式 [bx（或si、di）+idata]、[bx+si（或di）]
* 二重循环问题的处理————push CX pop CX
* 栈的应用————暂存数据
* 大小写转换的方法————and or
* and or指令