---
layout: post
title: "GPIO实验 点亮LED"
description: "gpio led exp"
category: embedded
tags: [embedded]
---
{% include JB/setup %}

###GPIO实验

目标：点亮LED

1.看原理图：GPF4=0 亮 GPF4=1 灭
                        
2.怎么让GPF4输出 0/1

看芯片手册操作寄存器

a. 配置功能：输出/输入/其他     配置GPFCON[9:8]=0b01     (0b表示二进制)
     
b.	设置输出高/低电     GPFDAT[4]=0

汇编实现：

```
@******************************************************************************
@ File：led_on.S
@ 功能：LED点灯程序，点亮LED1
@******************************************************************************       
            
.text
.global _start
_start:     
            LDR     R0,=0x56000050      @ R0设为GPFCON寄存器。此寄存器
                                        @ 用于选择端口B各引脚的功能：
                                        @ 是输出、是输入、还是其他
            MOV     R1,#0x00000100      @ 设置[9:8]=01 [0-7]=0000000 二进制为100000000B = 0x100  
            STR     R1,[R0]             @ 设置GPF4为输出口, 位[8:7]=0b01
            LDR     R0,=0x56000054      @ R0设为GPBDAT寄存器。此寄存器
                                        @ 用于读/写端口B各引脚的数据
            MOV     R1,#0x00000000      @ 此值改为0x00000010,
                                        @ 可让LED1熄灭
            STR     R1,[R0]             @ GPF4输出0，LED1点亮
MAIN_LOOP:
            B       MAIN_LOOP


```

Makefile:

```
led_on.bin : led_on.S
	arm-linux-gcc -g -c -o led_on.o led_on.S
	arm-linux-ld -Ttext 0x0000000 -g led_on.o -o led_on_elf
	arm-linux-objcopy -O binary -S led_on_elf led_on.bin
clean:
	rm -f   led_on.bin led_on_elf *.o
```

------
###arm汇编指令:

```
.data     @数据段声明 

.text     @代码段声明

.global _start     @指定函数入口 
LDR     R0,=0x56000050      @装载寄存器，0x56000050为GPFCON寄存器
STR R1,[R0]     @将R1寄存器的值写入R0地址的寄存器中

MAIN_LOOP:
            B       MAIN_LOOP     @B是跳转指令，这里是死循环

```

------
C实现：

```
#define GPFCON      (*(volatile unsigned long *)0x56000050)
#define GPFDAT      (*(volatile unsigned long *)0x56000054)

int main()
{
    GPFCON = 0x00000100;    // 设置GPF4为输出口, 位[8:7]=0b01
    GPFDAT = 0x00000000;    // GPF4输出0，LED1点亮

    return 0;
}

```

Makefile:

```
led_on_c.bin : crt0.S  led_on_c.c
	arm-linux-gcc -g -c -o crt0.o crt0.S
	arm-linux-gcc -g -c -o led_on_c.o led_on_c.c
	arm-linux-ld -Ttext 0x0000000 -g  crt0.o led_on_c.o -o led_on_c_elf
	arm-linux-objcopy -O binary -S led_on_c_elf led_on_c.bin
	arm-linux-objdump -D -m arm  led_on_c_elf > led_on_c.dis
clean:
	rm -f led_on_c.dis led_on_c.bin led_on_c_elf *.o

```

C程序：
volatile     告诉编译器不要优化

例子：

```
int a = 5;
int *p;
p = &a;
*p = 6; //与a = 6;一样
```

(\*(volatile unsigned long \*)0x56000050)
最外层(\*.....)代表取值，相当于\*p

------
按位操作：

1.	清零 位与
例：清除bit 3为0	`a = a & ~(1 << 3)`

2.	置1 位或
例：置bit 3为1		`a = a | (1 << 3)`

------
Makefile中：

arm-linux-ld -Ttext 0x0000000 led_on.o -o led_on_elf

-Ttext 0x0000000指定代码段从0地址开始

Makefile命令能被执行的条件

1.目标不存在

2.依赖已更新

------
####2440启动方式

#####NAND启动：

1.Nand 前4K内容，会拷贝到SDRAM

2.CPU从0地址执行

#####NOR启动：（可以像内存一样读，不能像内存一样写）

1.0地址指向NOR

2.CPU从0地址读取执行

------
####source insight 快捷键
alt + . 返回之前的文件

网卡开关
ifconfig eth0 down
ifconfig eth0 up

------
####C编译步骤：

1.预处理

2.编译     .c -> .s

3.汇编     .s -> .o

4.链接     多个.o合并成一个可执行文件

------
###Troubleshooting:
make编译成功，但无法看到效果

检查Makefile中arm-linux-ld -Ttext 0x0000000 crt0.o leds.o -o leds_elf

先连接crt0.o (crt0.S编译后文件) 用于引导main函数

再连接leds.o (leds.c编译后文件)