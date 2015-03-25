---
layout: post
title: "grub引导Windows"
description: "grub load windows"
category: Linux
tags: [Linux]
---
{% include JB/setup %}

###先前自动生成的grub.cfg加载windows有些问题（黑屏进不去之类的）

###vi /boot/grub/grub.cfg
找到windows

```
menuentry "Windows 7 (loader) (on /dev/sda1)" --class windows --class os {
    insmod part_msdos
    insmod ntfs
    insmod ntldr
    set root='(hd0,msdos1)'
    search --no-floppy --fs-uuid --set=root 1EA0019AA0017A13
    ntldr ($root)/bootmgr
}
```
第一行不用改

`insmod ntldr`grub不会自动添加

去掉一些if....之类的判断

保留`search --no-floppy --fs-uuid --set=root 1EA0019AA0017A13` 其中最后的1EA0019AA0017A13用自己的，这里只是展示

最后使用`ntldr ($root)/bootmgr`