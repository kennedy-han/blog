---
layout: post
title: "烧写裸版程序-linux"
description: "embedded"
category: embedded
tags: [embedded]
---
{% include JB/setup %}

###串口通信工具
使用ckermit工具串口通信

$ sudo apt-get install ckermit

使用kermit之前，现在/home/$USER下创建名为：`.kermrc`配置文件

```
set line /dev/ttyS0 #如果是用USB转串口查看/dev/下，一般是ttyUSB0
set speed 115200
set carrier-watch off
set handshake none
set flow-control none
robust
set file type bin
set file name lit
set rec pack 1000
set send pack 1000
set window 5
```

执行 sudo kermit 运行

使用 `c`命令连接

按下 `ctrl + \` ，再按`c`，切换到kermit （再按 `c` 又可以切换回串口通信）

输入 `exit` 退出kermit

###troubleshooting
#####Sorry, you must SET LINE or SET HOST first
执行 > chmod 777 /dev/ttyS0

###使用dnw和u-boot烧写（usb下载）
启动u-boot，按空格进入菜单，linux会识别到新的USB设备

```
> lsusb

Bus 002 Device 006: ID 5345:1234 Owon PDS6062T Oscilloscope
>
```

u-boot选择烧写到NAND，之后处于等待状态

打开另一个会话执行

```
> sudo dnw leds.bin
```
dnw 默认烧写到0x30000000位置

切换NAND启动