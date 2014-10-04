---
layout: post
title: "Assembly 指令总结"
description: "assembly instruction"
category: Assembly
tags: [Assembly]
---
{% include JB/setup %}

##8086CPU的指令系统总结
8086CPU提供以下几大类指令：

###1. 数据传送指令
比如，mov、push、pop、pushf、popf、xchg 等，这些指令实现寄存器和内存、寄存器和寄存器之间的单个数据传送

###2. 算数运算指令
比如，add、sub、adc、sbb、inc、dec、cmp、imul、idiv、aaa等，这些指令实现寄存器和内存中的数据的算数运算。他们的执行结果影响标志寄存器的 sf、zf、of、cf、pf、af位

###3. 逻辑指令
比如，and、or、not、xor、test、shl、shr、sal、sar、rol、ror、rcl、rcr等，除了not指令外，他们的执行结果都影响标志寄存器的相应标志位

###4. 转移指令
可以修改IP，或同时修改CS和IP的指令统称为转移指令。转移指令分为以下几类：

1. 无条件转移指令，比如jmp
2. 条件转移指令，比如jcxz、je、jb、ja、jnb、jna等
3. 循环指令，比如loop
4. 过程，比如call、ret、retf
5. 中断，比如int、iret

###5. 处理机控制指令
这些指令对标志寄存器或其他处理机状态进行设置，比如，cld、std、cli、sti、nop、clc、cmc、stc、hlt、wait、esc、lock等都是处理机控制指令

###6. 串处理指令
这些指令对内存中的批量数据进行处理，比如，movsb、movsw、cmps、scas、lods、stos等。若要使用这些指令方便地进行批量数据的处理，则需要和rep、repe、repne等前缀指令配合使用