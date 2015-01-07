---
layout: post
title: "编译加载驱动"
description: "compile run driver"
category: embedded
tags: [embedded]
---
{% include JB/setup %}

###编译加载驱动

修改drivers_and_test/first_drv/Makefile

KERN_DIR改为内核目录

之后

```
make
```

生成first_drv.ko

再编译测试程序

```
arm-linux-gcc -o firstdrvtest firstdrvtest.c
```

在开发板上进入drivers_and_test/first_drv目录
执行：

```
insmod first_drv.ko
./firstdrvtest on
./firstdrvtest off
```

观察LED灯