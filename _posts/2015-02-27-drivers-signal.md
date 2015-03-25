---
layout: post
title: "驱动信号"
description: "drivers signal"
category: embedded
tags: [embedded]
---
{% include JB/setup %}

###驱动信号

####驱动不同实现的对比：

1. 查询：耗资源
2. 中断：read() 一直在等
3. poll：指定过期时间

####App和驱动访问方式：
* App主动去read

* 驱动提醒应用（异步通知 signal）

####进程间发信号：
kill -9 PID

发   9  接

kill -USR1 PID

kill -10 PID

要点：

1. 注册信号处理函数
2. 谁发？
3. 发给谁？
4. 怎么发？

####目标：按下按键时，驱动通知应用
1. App：注册信号处理函数
2. 谁发：驱动
3. 发给谁：App -->App要告诉驱动PID
4. 怎么发：kill_fasync