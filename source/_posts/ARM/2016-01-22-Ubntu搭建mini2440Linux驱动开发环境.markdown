---
layout: post
title: "Ubntu搭建mini2440Linux驱动开发环境"
date: 2016-01-22 21:39:40 +7910
comments: true
categories: ARM mini2440
keywords: ARM,mini2440,博客,AnnPeter's Blog
description: "Ubntu搭建mini2440Linux驱动开发环境"
zTree: true

---

本文主要介绍在Ubuntu下开发Linux驱动程序环境的搭建过程。
<!-- more -->

####首先安装并配置串口通信工具minicom
1. 安装minicom

	```vim
	$ sudo apt-get install minicom
	```

2. 连上开发板，使用串口转USB转接头连上电脑, 查看/dev下的设备，我们可以发现一个以ttyUSB开头的设备，如图

	![串口设备图](/upload/2016/JAN/27/imgs/1453923495.png)

3. 配置minicom
```vim
$ sudo minicom -s
```
选择**Serial port setup**，注意设置A和F两项，设置只要按下A或F即可；设置完成返回，保存为默认选项**Save setup as dfl **。

![minicom配置图](/upload/2016/JAN/27/imgs/1453924021.png)

>在使用minicom进行通信的过程中，由于minicom没有正常退出，可能在下次启动minicom的时候可能出现**Device /dev/ttyUSB0 is locked.**<br>
><br>
>解决办法，我们只需要将/var/lock中相关的文件(问件名根据设备的不同可能有所不同)删除即可，实在不行，可以考虑重启系统。<br>


####除了minicom我们还可以使用kermit
1. 安装kermit

	```vim
	$ sudo apt-get install ckermit
	```
2. 配置文件/etc/kermit/kermrc, 添加黄框中的内容

	```vim
	$ sudo vim /etc/kermit/kermrc
	```
	![kermit配置文件修改](/upload/2016/JAN/27/imgs/1453925967.png)

####源码安装dnw工具
(1) 首先安装libusb-dev这个库

	```vim
	$ sudo apt-get install libusb-dev
	```

(2) 编译安装dnw，以下是dnw源码

```cpp
/* dnw2 linux main file. This depends on libusb.
*
* License: GPL
*
*/

#include <stdio.h>
#include <unistd.h>
#include <sys/stat.h>
#include <fcntl.h>
#include <usb.h>
#include <errno.h>

#define MINI2440_SECBULK_IDVENDOR 0x5345
#define MINI2440_SECBULK_IDPRODUCT 0x1234

struct usb_dev_handle * open_port(void)
{
	struct usb_bus *busses, *bus;
	struct usb_device *udev;
	struct usb_dev_handle *handle;

	usb_init();
	usb_find_busses();
	usb_find_devices();

	busses = usb_get_busses();
	for (bus = busses; bus; bus = bus->next) {
		for (udev = bus->devices; udev; udev = udev->next) {
			if (udev->descriptor.idVendor == MINI2440_SECBULK_IDVENDOR
					&& udev->descriptor.idProduct == MINI2440_SECBULK_IDPRODUCT) {
				printf("Target usb device found!\n");

				handle = usb_open(udev);
				if (!handle)
					perror("Cannot open device");
				else {
					if (usb_claim_interface(handle, 0) != 0) {
						perror("Cannot claim interface");
						usb_close(handle);
						handle = NULL;
					}
				}

				return handle;
			}
		}
	}

	printf("Target usb device not found!\n");
	return NULL;
}

void usage(void)
{
	printf("Usage: dnw2 [filename]\n\n");
}

unsigned char* prepare_write_buf(const char *filename, unsigned int *len)
{
	unsigned char *write_buf = NULL;
	struct stat stat;
	int fd;

	fd = open(filename, O_RDONLY);
	if (fd < 0) {
		perror("Cannot open file");
		return NULL;
	}
	if (fstat(fd, &stat) < 0) {
		perror("Cannot get file size");
		goto error;
	}

	write_buf = (unsigned char*)malloc(stat.st_size + 10);
	if (!write_buf) {
		perror("malloc faild");
		goto error;
	}

	if (read(fd, write_buf + 8, stat.st_size) != stat.st_size)
	{
		perror("Reading file failed");
		goto error;
	}
	printf("Filename : %s\n", filename);
	printf("Filesize : %ld bytes\n", stat.st_size);
	*((u_int32_t*)write_buf) = 0x30000000; //download address
	*((u_int32_t*)write_buf+1) = stat.st_size + 10; //download size;
	*len = stat.st_size + 10;

	return write_buf;

error:
	if (fd >= 0) close(fd);
	if (write_buf) free(write_buf);
	stat.st_size = 0;
	return NULL;
}

int main(int argc, char *argv[])
{
	struct usb_dev_handle *handle;
	unsigned char *write_buf = NULL;
	unsigned int len = 0, remain, towrite;

	if (argc != 2) {
		usage();
		exit(1);
	}

	handle = open_port();
	if (!handle)
		exit(1);

	write_buf = prepare_write_buf(argv[1], &len);
	if (!write_buf)
		exit(1);

	remain = len;
	printf("Writing data ...\n");
	while (remain) {
		towrite = remain > 512 ? 512 : remain;
		if(usb_bulk_write(handle, 0x03, write_buf + (len-remain), towrite, 3000) != towrite) {
			perror("usb_bulk_write failed");
			break;
		}
		remain -= towrite;
		printf("\r%d\t %d bytes ", (len-remain)*100/len, len-remain);
		fflush(stdout);
	}
	if(remain == 0) printf("Done!\n");

	if (write_buf) free(write_buf);

	usb_release_interface(handle, 0);
	usb_close(handle);

	return 0;
}

```

(3) 编译,安装
```vim
$ sudo gcc -o dnw dnw.c -lusb
```

####安装交叉工具链
```vim
$ tar xvzf arm-linux-gcc-4.3.2.tgz -C /
//解压后在/usr/local/下

$ cp -r arm /usr/local/
//现在交叉编译程序集都在/usr/local/arm/4.3.2/bin下了

//修改i/ect/profile,增加环境变量
export PATH="$PATH:/usr/local/arm/4.3.2/bin"
$source /etc/profile
```
