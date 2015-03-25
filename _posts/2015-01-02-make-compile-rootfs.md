---
layout: post
title: "制作烧写跟文件系统"
description: "烧写 裸板"
category: embedded
tags: [embedded]
---
{% include JB/setup %}

###制作、烧写跟文件系统

mkyaffs2image从tools工具文件夹取得，放到/bin下并且给执行权限

```
chmod +x /bin/mkyaffs2image
```

```
mkdir tmp
cp fs_mini_mdev.tar.bz2 tmp
cd tmp
sudo tar xjf fs_mini_mdev.tar.bz2		//解压
mkyaffs2image fs_mini_mdev fs.yaffs2	//使用mkyaffs2image工具对fs_mini_mdev目录生成镜像fs.yaffs2
```

####开发板上使用nfs下载fs.yaffs2
```
nfs 30000000 10.0.0.104:/work/nfs_root/tmp/fs.yaffs2
之后跟tftp下载后一样，擦除、烧写
nand erase root
nand write.yaffs 30000000 260000 $(filesize)

之后reset重启
```