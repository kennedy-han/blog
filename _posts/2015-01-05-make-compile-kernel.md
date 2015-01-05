---
layout: post
title: "制作烧写内核"
description: "make compile kernel"
category: embedded
tags: [embedded]
---
{% include JB/setup %}

###重烧系统：uboot，内核，文件系统
####u-boot的烧写和烧写裸板是一样的
#### 烧写内核：dnw，tftp
#### 在菜单输入k，然后使用dnw发送文件

或者使用

tftp:

```
tftp 30000000 uImage
nand erase kernel
nand write.jffs2 30000000 kernel
```

nfs:

```
nfs 30000000 10.0.0.104:/work/system/linux-2.6.22.6/arch/arm/boot/uImage
nand erase kernel
nand write.jffs2 30000000 kernel
```

tftp与nfs都是下载功能，下载到0x30000000后，还需要擦除、写入NAND