---
layout: post
title: "触摸屏驱动"
description: "触摸屏 驱动"
category: embedded
tags: [embedded]
---
{% include JB/setup %}

###触摸屏驱动

2440一上电，会把用不到的功能先关掉（SoC）

使用CLKCON寄存器

```
demsg
S3C244X:core 400.000 MHz, memory 100.000MHz, peripheral 50.000MHz
          FCLK CPU工作时钟          HCLK                PCLK                                                         
```

测试2nd~7th：

1. make menuconfig 去掉原来的触摸屏驱动程序

    ```
    Location:                                                             |
      |     -> Device Drivers                                                   |
      |       -> Input device support                                           |
      |         -> Generic input layer (needed for keyboard, mouse, ...) (INPUT |
      |           -> Touchscreens (INPUT_TOUCHSCREEN [=y])
                                            < >   S3C2410/S3C2440 touchscreens
    ```


    make uImage
    
    nfs 30000000 10.0.0.104:/work/nfs_root/tmp/fs_mini_mdev/uImage_nots
    bootm 30000000
    使用新内核启动

2. insmod s3c_ts.ko
按下/松开触摸笔

------

1. ls /dev/event*
2. insmod s3c_ts.ko
3. ls /dev/event*
4. hexdump /dev/event0 (event0是新加载的)

```
                         秒       微秒         
0000000 002d 0000 84a5 0002 0003 0000 029e 0000
0000010 002d 0000 84b9 0002 0003 0001 0338 0000
0000020 002d 0000 84be 0002 0001 014a 0001 0000
0000030 002d 0000 84c1 0002 0003 0018 0001 0000
0000040 002d 0000 84c4 0002 0000 0000 0000 0000
0000050 002d 0000 bf39 0002 0003 0000 0296 0000
0000060 002d 0000 bf4f 0002 0003 0001 0339 0000
0000070 002d 0000 bf56 0002 0000 0000 0000 0000
0000080 002d 0000 f9c9 0002 0003 0000 028b 0000
0000090 002d 0000 f9da 0002 0003 0001 033a 0000
00000a0 002d 0000 f9e0 0002 0000 0000 0000 0000
```

------
解决段错误：

加载lcd.ko出现段错误，要重新编译lcd.c驱动