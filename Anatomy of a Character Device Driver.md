---
tags:
  - embeddedLinux
  - deviceDrivers
---
#### Anatomy of a Character Device Driver
The following is an example device driver called `dummy`, which creates four devices that can be accessed through `/dev/dummy0` to `/dev/dummy3`.
```c
/* ------------------------------------------------------------------------- */
/*                                                                           */
/* Dummy char driver                                                         */
/*                                                                           */
/* Copyright (C) 2017, Chris Simmonds (chris@2net.co.uk)                     */
/*                                                                           */
/* This program is free software; you can redistribute it and/or modify      */
/* it under the terms of the GNU General Public License as published by      */
/* the Free Software Foundation; either version 2 of the License, or         */
/* (at your option) any later version.                                       */
/*                                                                           */
/* This program is distributed in the hope that it will be useful,           */
/* but WITHOUT ANY WARRANTY; without even the implied warranty of            */
/* MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU          */
/* General Public License for more details.                                  */
/*                                                                           */
/* You should have received a copy of the GNU General Public License         */
/* along with this program; if not, write to the Free Software               */
/* Foundation, Inc., 59 Temple Place, Suite 330, Boston, MA 02111-1307 USA   */
/*                                                                           */
/* ------------------------------------------------------------------------- */

#include <linux/kernel.h>
#include <linux/module.h>
#include <linux/init.h>
#include <linux/fs.h>
#include <linux/device.h>

#define DEVICE_NAME "dummy"
#define MAJOR_NUM 42
#define NUM_DEVICES 4

static struct class *dummy_class;

static int dummy_open(struct inode *inode, struct file *file)
{
	pr_info("%s\n", __func__);
	return 0;
}

static int dummy_release(struct inode *inode, struct file *file)
{
	pr_info("%s\n", __func__);
	return 0;
}

static ssize_t dummy_read(struct file *file,
			char *buffer, size_t length, loff_t * offset)
{
	pr_info("%s %u\n", __func__, length);
	return 0;
}

static ssize_t dummy_write(struct file *file,
			 const char *buffer, size_t length, loff_t * offset)
{
	pr_info("%s %u\n", __func__, length);
	return length;
}

struct file_operations dummy_fops = {
	.owner = THIS_MODULE,
	.open = dummy_open,
	.release = dummy_release,
	.read = dummy_read,
	.write = dummy_write,
};

int __init dummy_init(void)
{
	int ret;
	int i;

	printk("Dummy loaded\n");
	ret = register_chrdev(MAJOR_NUM, DEVICE_NAME, &dummy_fops);
	if (ret != 0)
		return ret;

	dummy_class = class_create(THIS_MODULE, DEVICE_NAME);
	for (i = 0; i < NUM_DEVICES; i++) {
		device_create(dummy_class, NULL,
			      MKDEV(MAJOR_NUM, i), NULL, "dummy%d", i);
	}

	return 0;
}

void __exit dummy_exit(void)
{
	int i;

	for (i = 0; i < NUM_DEVICES; i++) {
		device_destroy(dummy_class, MKDEV(MAJOR_NUM, i));
	}
	class_destroy(dummy_class);

	unregister_chrdev(MAJOR_NUM, DEVICE_NAME);
	printk("Dummy unloaded\n");
}

module_init(dummy_init);
module_exit(dummy_exit);
MODULE_LICENSE("GPL");
MODULE_AUTHOR("Chris Simmonds");
MODULE_DESCRIPTION("A dummy driver");
```

When the module is loaded, the `dummy_init()` function is called. The point at which it becomes a character device is when the call to `register_chrdev` happens, passing a pointer to struct `file_operations`, which contains pointers to the four functions that the driver implements. While `register_chrdev` tells the kernel that there is a driver with a major number of `42`, it doesn't say anything about the class of driver, and so it will not create an entry in `/sys/class`.

Without an entry in `/sys/class`, the device manager cannot create device nodes. So, the next few lines of code create a device class, dummy, and four devices of that class called `dummy0` to `dummy3`. The result is that the `/sys/class/dummy` directory is created when the driver is initialized, containing subdirectories `dummy0` to `dummy3`. Each of the subdirectories contains a file, `dev`, that contains the major and minor numbers of the device. This is all that a device manager needs to create device nodes: `/dev/dummy0` to `/dev/dummy3`. 

The `dummy_exit()` function has to release the resources claimed by `dummy_init()`, which here means freeing up the device class and major number. 

The file operations for this driver are implemented by `dummy_open()`, `dummy_read()`, `dummy_write()`, and `dummy_release()`, and they are called when a user space program calls `open(2)`, `read(2)`, `write(2)`, and `close(2)`, respectively.