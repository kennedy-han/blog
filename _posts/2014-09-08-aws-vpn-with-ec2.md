---
layout: post
title: "AWS VPN with EC2"
description: "aws vpn"
category: aws vpn
tags: [aws]
---
{% include JB/setup %}

##在Mac上连接到vpn但是不能访问外网的解决方法

通过Mac自带的vpn客户端可以连接到vpn服务器，但是不能打开任何的网站。google了很久才找到解决方案如下

系统设置->网络->Wi-Fi->高级->硬件

配置选项改为手动

MTU改为1450

http://chylvina.com/2012/06/10/aws-ec2-vpn/
http://www.yzhang.net/blog/2013-03-07-pptp-vpn-ec2.html
http://www.cnblogs.com/sixiweb/archive/2012/11/20/2778732.html
