---
layout: post
title: "LCD 驱动"
description: "LCD drivers"
category: embedded
tags: [embedded]
---
{% include JB/setup %}

###LCD驱动程序

假设
app:     open("/dev/fb0", ...) 主设备号：29，次设备号：0

------

kernel:

```
               fb_open
                    int fbidx = iminor(inode);
                    struct fb_info *info = registered_fb[0];
```
                   
app:     read()

------

kernel:

```
               fb_read
                    int fbidx = iminor(inode);
                    struct fb_info *info = registered_fb[fbidx];
                    if (info->fbops->fb_read)
                         return info->fbops->fb_read(info, buf, count, ppos);
                   
                    src = (u32 __iomem *) (info->screen_base + p);
                    dst = buffer;
                    *dst++ = fb_readl(src++);
                    copy_to_user(buf, buffer, c)
```                    

------

问1 : registered_fb在哪里被设置？

答1 . register_framebuffer

###怎么写LCD驱动程序？
1. 分配一个fb_info结构体：framebuffer_alloc
2. 设置
3. 注册：register_framebuffer
4. 硬件相关的操作

####2440芯片手册，只关心TFT类型的LCD类型，STN是另一款



1. make menuconfig 去掉原来的驱动程序

	```
-> Device Drivers
     -> Graphics support
<M> S3C2410 LCD framebuffer support
	```

2. make uIamge
     make modules
    
3. 使用新的uImage启动开发板

	```
nfs 30000000 10.0.0.104:/work/nfs_root/tmp/fs_mini_mdev/uImage_noLCD
bootm 30000000
	```

	之后挂接nfs

	```
	mount -t nfs -o nolock,vers=2 10.0.0.104:/work/nfs_root/	tmp/fs_mini_mdev /mnt
	```

	若不成功

	```
	ifconfig eth0 up
	ifconfig eth0 10.0.0.111
	```

4. 载入驱动

	```
insmod cfbcopyarea.ko
insmod cfbfillrect.ko
insmod cfbimgblt.ko
insmod lcd.ko
	```

	```
	echo hello > /dev/tty1     //可以在LCD上看见hello
	cat lcd.ko > /dev/fb0          //花屏
	```

5. 修改/etc/inittab

加入：

`tty1::askfirst:~/bin/sh`

用新内核重启开发板

```
insmod cfbcopyarea.ko
insmod cfbfillrect.ko
insmod cfbimgblt.ko
insmod lcd.ko
insmod buttons.ko     (输入子系统)
```