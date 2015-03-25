---
layout: post
title: "Linux自动挂载"
description: "Linux auto mount"
category: Linux
tags: [Linux]
---
{% include JB/setup %}

###症状：
NTFS分区的硬盘无法自动挂载，U盘不能自动挂载、CD不能自动挂载

检查/dev下有设备

###解决：
```
sudo vi /etc/fastab
```
