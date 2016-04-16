---
layout: post
title: "NorFlash和LandFlash"
date: 2016-01-14 23:09:14 +1090
comments: true
categories: ARM mini2440
keywords: ARM,mini2440,博客,AnnPeter's Blog
description: "NorFlash和LandFlash"
zTree: true

---

本文主要介绍Nor Flash 和 Land Flash的区别。
<!-- more -->

1. 开发板上一般都有SDRAM做内存，flash(nor、nand)来当做ROM。其中nand flash一次至少读取一页(512B)
2. nand flash不用来运行代码，只用来存储代码； nor flash、SDRAM可以直接运行代码
3. s3c2440对外引出了27根地址线ADDR0～ADDR26，它最多能够寻址128M；而s3c2440的寻址空间可以达到1GB，这是由于s3c2440将1GB的地址空间分成了8个Banks(Bank0~Bank7，bank0~bank5为固定128MB,bank6和bank7的容量可编程改变，可以是2、4、8、16、32、64、128MB)，其中每个Bank对应一根片选信号nGCS0~nGCS7，当访问Bankx的时候，nGCSx管脚电平拉低，用来选中外接设备，s3c2440通过8根片选信号线和27根地址线，就可以访问1GB。


	![内存Bank分配](/upload/2016/JAN/27/imgs/1453968568.png)

4. s3c2440支持两种启动模式：NAND和非NAND(这里是nor flash)
	具体采用的方式取决于OM0、OM1两个引脚
    OM[1:0]=00时，处理器从nand flash启动
    OM[1:0]=01时，处理器从16位宽度的ROM启动
    OM[1:0]=10时，处理器从32位宽度的ROM启动
	OM[1:0]=11时，处理器从Test Mode启动


5. 当从nand启动时
	cpu会自动从nand flash中(地址0x3000,0000)读取前**4KB**的数据放置在片内SRAM中，同时把这段SRAM映射到nGCS0(第一个内存Bank，2440共有8个Banks)片选的空间(即0x0000,0000)。CPU是从**0x0000,0000**开始执行，也就是nand flash里面的前4KB的内容。

	下图是nand flash存储器映射

	![NAND Flash 存储器映射](/upload/2016/JAN/27/imgs/1453969765.png)

6. 当从非nand flash启动时
	nor flash被映射到0x0000,0000地址(就是nGCS0，这里就无需SRAM来辅助了，可以直接访问nor flash，所以片内SRAM的起始地址0x4000,0000).然后cpu从0x0000,0000开始执行。
