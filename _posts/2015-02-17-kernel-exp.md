---
layout: post
title: "内核实验"
description: "kernel exp"
category: embedded
tags: [embedded]
---
{% include JB/setup %}

###内核

###最终目的：执行应用程序

1. 解压缩
2. 打补丁：patch -p? < 补丁
3. 配置

	```
     make menuconfig
     使用默认配置在上面修改
          在arch/arm/configs 找到相似的配置文件 xxx_defconfig
          make xxx_defconfig
          make menuconfig
     使用厂家提供的配置文件
          cp config_厂家 .config
          make menuconfig
	```

4. 编译

	```
     make
     make uImage
	```

####配置结果：生成 .config文件

Makefile中
a.c 和 b.c 组成一个模块：

```
obj_m += ab.o
ab_objs := a.o b.o
```

第一个文件：arch/arm/kernel/head.S

链接脚本：arch/arm/kernel/vmlinux.lds

####head.S所做的事：

处理u-boot传入的参数（机器ID，启动参数）

0. 判断是否支持这个CPU
1. 判断是否支持这个单板（u-boot启动内核时传入的机器ID）
2. 建立页表
3. 使能MMU
4. 跳到"start_kernel"

####挂接：根文件系统

####内核怎样启动第1个APP：
1. open(/dev/console)
2. run_init_process
     命令行init="xxx"
     /sbin/init
     ...

####init程序：
1. 读取配置文件
2. 解析配置文件
3. 执行（用户程序）

####配置文件：
1. 指定程序
2. 何时执行

####最小根文件系统：
1. /dev/console
2. init-->busybox
3. /etc/inittab
4. 配置文件指定的程序
5. C库

####使用busybox构建根文件系统
busybox:ls、cp、cd...等命令是一个链接文件

ls --> busybox （busybox ls）

cp --> busybox


```
make uImage V=1
V=1会把命令更加详细列出
```

```
arm-linux-ld -EL  -p --no-undefined -X -o vmlinux -T arch/arm/kernel/vmlinux.lds arch/arm/kernel/head.o arch/arm/kernel/init_task.o
```

####第一个文件：arch/arm/kernel/head.o

####链接脚本：arch/arm/kernel/vmlinux.lds

###内核启动流程：
arch/arm/kernel/head.S

```
start_kernel
          setup_arch                         //解析u-boot传入的启动参数
          setup_command_line     //解析u-boot传入的启动参数
          parse_eraly_param
                    do_early_param
                              从__setup_start到__setup_end。调用early函数
          unknown_bootoption
                    obsolete_ckecksetup
                              从__setup_start到__setup_end。调用非early函数
          rest_init
                    kernel_init
                              prepare_namespace
                                        mount_root          //挂接根文件系统
                              init_post
                                        //执行应用程序
                                       
```
