---
layout: post
title: "Ubuntu 网络没有 eth0"
description: "Ubuntu net eth0"
category: Linux
tags: [Linux]
---
{% include JB/setup %}

###问题：ifconfig -a之后看不到eth0设备

###解决：
用lspci 显示有network 说明不用编译模块

```
sudo vi /etc/network/interfaces
```
```
//通过dhcp动态设置
iface eth0 inet dhcp
```
