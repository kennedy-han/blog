---
layout: post
title: "LED按键中断"
description: "LED type irq"
category: embedded
tags: [embedded]
---
{% include JB/setup %}

###LED按键中断


####中断方式获取键值：
单片机：

1. 按键按下
2. CPU发生中断，跳到异常向量入口执行
3. b 函数
     1. 保存被中断的现场
     2. 执行中断处理函数
     3. 恢复

Linux：

1. 异常向量

	```
     trap int 构造
          b ...
          b vector_irq+offset
	```

2. vector_irq:

	```
     用宏实现
     保存现场
     do_asm_IRQ
     恢复现场
	```
	
####单片机下的中断处理：
1. 分辨是哪个中断
2. 调用处理函数
3. 清中断

####Linux：

```
asm_do_IRQ
desc->handle_irq(  )
irq_desc
```

handle_edge_irq:

```
1. desc->chip->ack(irq)     :清中断
2. handle_IRQ_event          :处理中断
     取出action链表中的成员
     执行：action->handler
```

####按下按键后，会触发：
1. 进入异常模式
     b vector_irq+偏移
2. __irq_usr
3. b asm_do_IRQ
4. irq_desc(irq中断号)->handle_irq
5. handle_edge_irq

####注册中断程序：
request_irq(irq, handle, irqflags, devname, dev_id);

1. 分配irqaction结构
2. setup_irq(irq, action)
     1. 在irq_desc(I irq)->action链表加入
     2. desc->chip->settype( )	设置引脚
     3. desc->chip->startup / enable  使能中断

####卸载中断：
free_irq(irq, dev_id);

出链表

禁止中断

