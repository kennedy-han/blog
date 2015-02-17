---
layout: post
title: "根文件系统 实验"
description: "rootfs exp"
category: embedded
tags: [embedded]
---
{% include JB/setup %}

###根文件系统

busybox -> init_main

```
parse_inittab file = fopen(INITTAB, "r");     //打开配置文件/etc/inittab                                            

new_init_action          //1创建一个init_action结构，填充
                                                                                          //2把这个结构放入init_action_list链表
                                            
run_actions(SYSINIT);
waitfor(a, 0);          //执行应用程序，等待它执行完毕
run(a);                    //创建process子进程
waitpid(runpid, &status, 0);     //等待它结束
delete_init_action(a);     //在init_action_list链表里删除
run_actions(WAIT);
run_actions(ONCE);
```                                  

###inittab格式：
```<id>:<runlevels>:<action>:<process>```

id => /dev/id，用作终端：stdin,stdout,stderr：printf,scanf,err

runlevels：忽略

action：执行时机

process：应用程序或脚本

------
```
$ ls /dev/console /dev/null -l
crw------- 1 root root 5, 1 2015-01-25 10:11 /dev/console
crw-rw-rw- 1 root root 1, 3 2015-01-25 09:58 /dev/null
```

c代表字符设备 5主设备号 1次设备号

####proc虚拟文件系统

mount -t proc none /proc

mount -a 根据etc/fstab挂载