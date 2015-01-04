---
layout: post
title: "dnw替代方法"
description: "uboot without dnw"
category: embedded
tags: [embedded]
---
{% include JB/setup %}

###u-boot菜单中的download都是用dnw使用USB下载

###Win7下无法使用dnw替代方法：
1. 网络下载：tftp、nfs （之前有介绍）

2. Linux下的dnw

把linux下的dnw程序放到/bin目录

```
sudo chmod +x /bin/dnw
```

先进入u-boot菜单

####如果使用Vmware，让Vmware位于最前面，然后再用USB线连接PC和开发板USB DEVICE接口

用`lsusb`命令确认vmware linux已经识别出UBOOT的USB设备

```
> lsusb

Bus 002 Device 003: ID 5345:1234 Owon PDS6062T Oscilloscope
```

####需要开启VMware USB Arbitration Service服务

虚拟机启动后，右下角usb连接

* 在UBOOT界面输入k
     然后在Linux下执行 dnw uImage
    
* 在UBOOT界面输入y
     然后在Linux下执行 dnw fs_qtopia.yaffs2