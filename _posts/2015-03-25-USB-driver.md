---
layout: post
title: "USB驱动"
description: "USB 驱动"
category: embedded
tags: [embedded]
---
{% include JB/setup %}

###USB驱动

####现象：把USB设备接到PC
1. 右下角弹出"发现android phone"
2. 跳出一个对话框，提示安装驱动程序

问1. 既然还没有"驱动程序"，为何能知道是"android phone"

答1. windows里已经有了USB的驱动程序，接入USB设备后，是"总线驱动程序"知道你是"android phone"
               提示你安装的是"设备驱动程序"
              
          USB总线驱动程序负责：识别USB设备，给USB设备找到
         
问2. USB设备种类非常多，为什么一接入电脑，就能识别出来？

答2. PC和USB设备都得遵守一些规范
          比如：USB设备接入电脑后，PC机会发出"你是什么"?
                         USB设备就必须回答"我是xxx"，并且回答的语言必须是中文
          USB总线驱动程序会发出某些命令，想获取设备信息(描述符)
          USB设备必须返回"描述符"给PC

问3. PC机上接有非常多的USB设备，怎么分辨他们？

               USB接口只有4条线：5V,GND,D-,D+
               
答3. 每一个USB设备接入PC时，USB总线驱动程序都会给它分配一个编号
               接在USB总线上的每一个USB设备都有自己的编号(地址)
               PC机想访问某个USB设备时，发出的命令都含有对应的编号(地址)
              
问4. USB设备刚接入PC时，还没有编号，那么PC怎么把"分配的编号"告诉它？

答4. 新接入的USB设备的默认编号是0，在未分配新编号前，PC使用0编号和它通信。

问5. 为什么一接入USB设备，PC机就能发现它？

答5. PC的USB口内部，D-和D+接有15K的下拉电阻，未接USB设备时为低电平
               USB设备的USB口内部，D-或D+接有1.5K的上拉电阻；它一接入PC，就会把PC USB口的D-或D+拉高，从硬件的角度通知PC有新设备接入
    
          
####其他概念：
1. USB是主从结构的
          所有的USB传输，都是从USB主机这方发起；USB设备没有"主动"通知USB主机的能力
          例子：USB鼠标滑动一下立刻产生数据，但是它没有能力通知PC机来读数据，只能被动的等待PC机来读。
         
2. USB的传输类型：

     a. 控制传输：可靠，时间有保证，比如：USB设备的识别过程
     
     b. 批量传输：可靠,时间没有保证,比如：U盘
     
     c. 中断传输(不断访问)：可靠，实时，比如：USB鼠标
     
     d. 实时传输：不可靠，实时，比如：USB摄像头
    
3. USB传输的对象：端点(endpoint)
          我们说"读U盘"、"写U盘"，可以细化为：把数据写到U盘的端点1，从U盘的端点2里读出数据
          除了端点0外，每一个端点只支持一个方向的数据传输
          端点0用于控制传输，既能输出也能输入
         
4. 每一个端点都有传输类型、传输方向

5. 术语里、程序里说的输入(IN)、输出(OUT) "都是"基于USB主机的立场说的。
          比如鼠标，鼠标的数据是从鼠标传到PC机，对应的端点成为"输入端点"
         
6. USB总线驱动程序的作用
     a.识别USB设备
     b.查找并安装对应的设备驱动程序
     c.提供USB读写函数

###USB驱动程序框架：

```
app:
----------------------------------
          USB设备驱动程序               //知道数据含义，拿到数据做什么事情由它决定
内核------------------------------
          USB总线驱动程序               //1.识别 2.找到匹配的设备驱动 3.提供USB读写函数(它不知道数据含义)
----------------------------------
          USB主机控制器
          UHCI OHCI EHCI
硬件------------------------------
              USB设备
```

------
####控制器类型

```
UHCI：intel，               低速(1.5Mbps)/全速(12Mbps)
OHCI: microsoft          低速/全速
EHCI：                              高速(480Mbps)
```

###USB总线驱动程序的作用
1. 识别USB设备

    1.1 分配地址
    
    1.2     并告诉USB设备(set address)
    
    1.3 发出命令获取描述符
描述符的信息可以在include\linux\usb\Ch9.h看到

2. 查找并安装对应的设备驱动程序

3. 提供USB读写函数

把USB设备接到开发板上，看输出信息

```
usb 1-1: new full speed USB device using s3c2410-ohci and address 2
usb 1-1: configuration #1 chosen from 1 choice
scsi0 : SCSI emulation for USB Mass Storage devices
scsi 0:0:0:0: Direct-Access     Kingston DT 101 G2        PMAP PQ: 0 ANSI: 0 CCS
sd 0:0:0:0: [sda] 15240576 512-byte hardware sectors (7803 MB)
sd 0:0:0:0: [sda] Write Protect is off
sd 0:0:0:0: [sda] Assuming drive cache: write through
sd 0:0:0:0: [sda] 15240576 512-byte hardware sectors (7803 MB)
sd 0:0:0:0: [sda] Write Protect is off
sd 0:0:0:0: [sda] Assuming drive cache: write through
sda: sda1
sd 0:0:0:0: [sda] Attached SCSI removable disk
```

拔掉

```
usb 1-1: USB disconnect, address 2
```

再接上：

```
usb 1-1: new full speed USB device using s3c2410-ohci and address 3
usb 1-1: configuration #1 chosen from 1 choice
scsi1 : SCSI emulation for USB Mass Storage devices
scsi 1:0:0:0: Direct-Access     Kingston DT 101 G2        PMAP PQ: 0 ANSI: 0 CCS
sd 1:0:0:0: [sda] 15240576 512-byte hardware sectors (7803 MB)
sd 1:0:0:0: [sda] Write Protect is off
sd 1:0:0:0: [sda] Assuming drive cache: write through
sd 1:0:0:0: [sda] 15240576 512-byte hardware sectors (7803 MB)
sd 1:0:0:0: [sda] Write Protect is off
sd 1:0:0:0: [sda] Assuming drive cache: write through
sda: sda1
sd 1:0:0:0: [sda] Attached SCSI removable disk
```

在内核目录下搜：

```
grep "USB device using" * -nR
usb/core/hub.c:2186:              "%s %s speed %sUSB device using %s and address %d\n",
```

```
hub_irq
     kick_khubd
          hub_thread
               hub_events
                    hub_port_connect_change
                   
                         udev = usb_alloc_dev(hdev, hdev->bus, port1);
                              dev->dev.bus = &usb_bus_type;
                        
                         choose_address(udev); //给新设备分配编号(地址)
                        
                         hub_port_init          //usb 1-1: new full speed USB device using s3c2410-ohci and address 3
                        
                              hub_set_address          //把编号(地址)告诉USB设备
                             
                              usb_get_device_descriptor(udev, 8);     //获取设备描述符
                                   retval = usb_get_device_descriptor(udev, USB_DT_DEVICE_SIZE);
                                  
                         usb_new_device(udev)    
                              err = usb_get_configuration(udev);     //把所有的描述符都读出来，并解析
                                   usb_parse_configuration
                                  
                              device_add     //把device放入usb_bus_type的dev链表
                                                            //从usb_bus_type的driver链表里取出usb_driver，一一比较
                                                            //把usb_interface和usb_driver的id_table比较
                                                            //如果能匹配，调用usb_driver的probe
 
```
                                                         
###怎么写USB设备驱动程序？
1. 分配/设置usb_driver结构体
                         .id_table
                         .probe
                         .disconnect
2. 注册

测试1st/2nd：

1. make menuconfig 去掉原来的USB鼠标驱动

    ```
    -> Device Drivers                                                   
        -> HID Devices
        < > USB Human Interface Device (full HID) support
    ```

2. make uImage 并使用新内核启动
    nfs 30000000 10.0.0.104:/work/nfs_root/tmp/fs_mini_mdev/uImage_nohid
    
    bootm 30000000

3. insmod usbmouse_as_key.ko
4. 在开发板上接入、拔出USB鼠标


测试3rd:

1. insmod usbmouse_as_key.ko
2. ls /dev/event*
3. 接上USB鼠标
4. ls /dev/event*
5. 操作鼠标观察数据

测试4th:

1. insmod usbmouse_as_key.ko
2. ls /dev/event*
3. 接上USB鼠标
4. ls /dev/event*
5. cat /dev/tty1     然后按鼠标键
6. hexdump /dev/event0

详见《LINUX内核源代码情景分析》