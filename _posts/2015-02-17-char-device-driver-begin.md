---
layout: post
title: "字符设备驱动程序 绪论"
description: "char device driver begin"
category: embedded
tags: [embedded]
---
{% include JB/setup %}

###字符设备驱动程序

####驱动框架：
1. 写出led_open、led_write函数
2. 怎么告诉内核
     1. 定义一个file_operations，填充
     2. 把这个结构告诉内核：register_chrdev
     3. 谁来调用：驱动的入口函数


####驱动：
自动分配主设备号

手工分配

应用：open("/dev/xxx")     怎么取？

手工创建：mknod /dev/xxx c 主 次

自动创建：udev，mdev 根据系统信息创建设备节点

写一个LED驱动
1.框架
2.完善硬件的操作
     1.看原理图、引脚
     2.看2440手册
     3.写代码
          1.单片机：物理地址
          2.驱动：虚拟地址-->用ioremap映射
配置GPFCON和GPFDAT寄存器操作LED

------

cat /proc/devices
查看设备，找空缺项确定主设备号

busybox mdev
/sys/class 各种类，找到驱动中定义的类


打开/dev/buttons设备，定义到5
exec 5</dev/buttons

cat /proc/interrupts
ls -l /proc/当前sh进程号/fd

关闭
exec 5<&-