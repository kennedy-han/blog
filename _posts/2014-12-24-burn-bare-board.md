---
layout: post
title: "烧写裸板程序"
description: "embedded"
category: embedded
tags: [embedded]
---
{% include JB/setup %}

###beginning
* Windows可使用secureCRT与开发板串口通信
    * 新建会话，终端选择serial
    * 端口选择我的电脑管理中，识别出的COM端口号
    * 波特率改为115200
    * 去掉RTS/CTS复选框
* 第一次使用（开发板没有u-boot）时，使用说明书上的工具（openJTAG、J-Link等）烧写u-boot到Nor Flash

* 切换到Nor Flash启动

* 按空格进入u-boot菜单

* 根据提示选择Download u-boot to Nor Flash

* 之后 Windows下可使用dnw下载程序到u-boot （win7的驱动有些问题），可使用tftp代替dnw

* 使用tftp需要查看和配置开发板IP 使用`print` 命令

* ipaddr和serverip属性，要和tftp的server interface中IP处于一个网段，可在u-boot菜单按Q退出后，使用
    ```
   set ipaddr 10.0.0.102 //设置开发板IP
   set serverip 10.0.0.100 //设置开发板将要连接的server ip
    ```
    
* 保存：`save`命令
   
* 之后在开发板上ping server ip （注意：不可在PC机上ping开发板，因为u-boot不回复）

###裸板烧写4种方法
1. 并口：使用并口工具烧写：接线，使用oflash烧写，重新上电观察效果

2. 使用openJTAG烧写：接线，使用oflash烧写，烧写后会自动复位开发板

3. JLINK只能烧写NOR Flash，烧好u-boot.bin，使用NOR启动

4. 使用Nor Flash上的u-boot烧写
    * 使用菜单通过USB下载烧写(接好USB下载线)
    * 使用TFTP下载烧写(接好网线)
        * 在tftp程序中制定current Directory为基准
        
        * 开发板上 `tftp 3000000 lcd.bin`
            * `help tftp`查看使用规则
        
        * `mtd`查看分区情况
        
        * 使用`nand erase bootloader` 擦除bootloader分区
        
        * 使用`nand write 30000000 bootloader`将地址30000000内容写入bootloader分区
        
        * 关电，切换Nand启动，再上电即可看到效果

###使用tftp下载程序到u-boot
* u-boot菜单选择烧写到NAND

* 在tftp程序中制定current Directory为基准

* 开发板上 `tftp 3000000 lcd.bin`
    * `help tftp`查看使用规则

* `mtd`查看分区情况

* 使用`nand erase bootloader` 擦除bootloader分区

* 使用`nand write 30000000 bootloader`将地址30000000内容写入bootloader分区

* 关电，切换Nand启动，再上电即可看到效果

###烧写到NAND测试程序
Nor启动进入u-boot后，退出菜单

使用tftp下载

tftp 30000000 lec_on.bin

擦除Nand

nand erase bootloader

nand write 30000000 bootloader

之后关电，切换Nand启动，上电，观察效果

###u-boot操作
* `q`退出菜单（退出菜单可使用print等命令）

* `menu`重新进入菜单

