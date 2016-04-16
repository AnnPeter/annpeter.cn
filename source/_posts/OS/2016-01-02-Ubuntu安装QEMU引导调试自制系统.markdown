---
layout: post
title: "Ubuntu安装QEMU引导调试自制系统"
date: 2016-01-02 20:17:09 +3082
comments: true
categories: OS
keywords: OS,QEMU,Ubuntu,博客,AnnPeter's Blog
description: "Ubuntu安装QEMU引导调试自制系统"
zTree: true

---


环境:Ubuntu15.10

1. 首先安装QEMU

2. 制作引导软盘镜像flp.img

<!-- more -->

```vim

方案一（比较麻烦）：
//用dd命令新建硬盘镜像disk.img
$ sudo dd if=/dev/zero of=disk.img bs=512 count=2880

//用losetup命令将flp.img与loop设备关联，这样我们就能通过/dev/loop0设备，像操作真实硬盘那样操作disk.img
$ sudo losetup /dev/loop0 disk.img

//将可执行文件boot中的引导代码写入disk.img的第一个扇区。
//使用dd命令，将boot中的可执行代码拷贝到disk.img
$ dd if=boot of=/dev/loop0 bs=512 count = 1

//借助QEMU从引导镜像启动系统
$ sudo qemu-system-x86_64 -boot order=a -fda /dev/loop0




方案二（简单）：
//第二步可直接省略用boot文件直接启动系统
$ qemu-system-i386 -fda boot


Tip:以上两个方案，如果要启动调试功能，请加上 -gdb tcp::1234 -S参数。（1234是端口号）
然后重新开启一个终端
$ gdb
target remote:1234
连接上就可以调试了
```
Tip:[各种调试技巧](http://blog.csdn.net/iamljj/article/details/5655169)