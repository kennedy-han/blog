---
layout: post
title: "Assembly 标志寄存器"
description: "assembly flag"
category: Assembly
tags: [Assembly]
---
{% include JB/setup %}

简称flag，flag寄存器是按位起作用的

有的指令的执行是影响标志寄存器的，如：add、sub、mul、div、inc、or、and等，他们大都是运算指令

有的指令的执行对标志寄存器没有影响，如：mov、push、pop等，他们大都是传送指令

具有以下3个作用：

1. 用来存储相关指令的某些执行结果
2. 用来为CPU执行相关指令提供行为依据
3. 用来控制CPU的相关工作方式

|15|14|13|12|11|10|9|8|7|6|5|4|3|2|1|0|
|-------------------------------------|
|  |  |  |  |OF|DF|IF|TF|SF|ZF||AF||PF||CF|

###ZF标志
flag的第6位是ZF，零标志位。它记录相关指令执行后，其结果是否为0。如果结果为0，zf=1；如果结果不为0，zf=0

例：

```
mov ax,1
sub ax,1
```
执行后，结果为0，则zf=1

###PF标志
flag的第2位是PF，奇偶标志位，它记录相关指令执行后，其结果的`所有bit位`中1的个数是否为偶数。

如果1的个数为偶数，则pf=1

如果为1的个数为奇数，则pf=0

例：

```
mov al,1
add al,10
```
执行后，结果为00001011B，其中有3（奇数）个1，则pf=0

###SF标志
flag的第7位是SF，符号标志位，他记录相关指令执行后，其结果是否为负

如果为负，sf=1

如果非负，sf=0

计算机通常用补码来表示有符号数据，计算机中的一个数据可以看做是有符号数，也可以看成无符号数

比如：

00000001B，可以看做无符号数1，或有符号数+1
10000001B，可以看做无符号数129，也可以看做有符号数-127

对于同一个二进制数，计算机可以将它当做无符号数据来运算，也可以当做有符号数据来运算，比如：

```
mov al,10000001B
add al,1
```
结果：10000010B

可以将add指令运算当做无符号数运算，那么add指令相当于计算129+1，结果为130（10000010B）；

也可以将add指令运算当做有符号运算，那么add指令相当于计算-127+1，结果为-126（10000010B）

SF标志，就是CPU对有符号数运算结果的一种记录，它记录数据的正负

例：

```
mov al,10000001B
add al,01111111B
```
执行后，结果为0，sf=0，结果为非负

###CF标志（无符号运算）
flag的第0位是CF，进位标志位，一般情况下，在进行无符号数运算的时候，它记录了运算结果的最高有效位向更高位的进位值

例：

```
mov al,98H
add al,al	;执行后：(al)=30H，CF=1，CF记录了从最高有效位向更高位的进位值
add al,al	;执行后：(al)=60H，CF=0，因为这步操作没有产生进位值
```

###OF标志（有符号运算）
flag的第11位是OF，溢出标志位。一般情况下，OF记录了有符号数运算的结果是否发生了溢出

如果发生溢出，OF=1

如果没有发生溢出，OF=0

在进行有符号数运算的时候，如结果超过了机器所能表示的范围称为溢出

对于8位的有符号数据，机器所能表示的范围就是-128~127

对于16位有符号数据，机器所能表示的范围是-32768~32767

如果运算结果超出了机器所能表达的范围，将产生溢出

注意，这里所讲的溢出，只是对有符号数运算而言

例：

```
mov al,0F0H
add,88H
```
add指令执行后：CF=1，OF=1。对于无符号数运算，OF0H(240)+88H(136)=376，超出0~255有进位，CF=1

对于有符号数，0F0H=-16，88H=-120，-16-120=-136超出-128~127范围，OF=1

###adc指令
adc是带进位加法指令，它利用了CF位上记录的进位值

例：

```
mov ax,2
mov bx,1
sub bx,ax
adc ax,1
```
执行后，(ax)=4。adc执行时，相当于计算：(ax)+1+CF=2+1+1=4

```
mov ax,1
add ax,ax
adc ax,3
```
执行后，(ax)=5。adc执行时，相当于计算：(ax)+3+CF=2+3+0=5

adc指令的意义：对任意大的数据进行加法运算