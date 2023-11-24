---
tags:
  - deviceDrivers
---
### Creating a Device Node
#### Device Identification
To precisely identify devices, their identifiers must be unique. Although identifiers can be dynamically allocated, most drivers still use static identifiers for compatibility reasons. Whatever the allocation method, the Linux kernel stores file device numbers in elements of `dev_t` type, which is a 32-bit unsigned integer in which the major is represented by the first 12 bits, and the minor is coded on the 20 remaining bits.

`include/linux/kdev_t.h` contains several macros, including those that, given a `dev_t` type variable, can return either a minor or a major number.
```c
#define MAJOR(dev) ((unsigned int) ((dev) >> MINORBITS))
#define MINOR(dev) ((unsigned int) ((dev) & MINORMASK))
#define MKDEV(ma,mi) (((ma) << MINORBITS) | (mi))
```
#### Registration of Character Device Numbers
There are two ways to deal with device numbers – **registration** (the static method) and **allocation** (the dynamic method).

Registration, also called static allocation, is only useful if you know in advance which major number you want to start with, after making sure it does not clash with another driver using the same major. It is a brute-force method in which you let the kernel know what device numbers you want by providing the starting major/minor pair and the number of minors, and it either grants them to you or not.
```c
int register_chrdev_region(dev_t first, unsigned int count, char *name);
```
This method returns `0` on success, or a negative error code when it fails. The first parameter is the identifier you must have built using the major number and the first minor of the desired range. `count` is the number of consecutive device minors required, and name should be the `name` of the associated device or driver.

Because this can be a source of conflict with other device drivers, it is considered preferable to use dynamic allocation, with which the kernel happily allocates a major number for you on the fly.
```c
int alloc_chrdev_region(dev_t *dev, unsigned int firstminor,
						unsigned int count, char *name);
```
This method returns `0` on success, or a negative error code on failure. dev is the only output parameter. It represents the first number that the kernel assigned. `firstminor` is the first of the requested range of minor numbers, `count` is the number of consecutive minors you need, and `name` should be the name of the associated device or driver.
#### Initializing and Registering a Character Device
- After the identifier has been allocated, you must initialize the character device and add it to the system using `cdev_init()` and `cdev_add()`, respectively.
```c
void cdev_init(struct cdev *cdev, const struct file_operations *fops);
int cdev_add (struct cdev * p, dev_t dev, unsigned count);
```
In `cdev_init()`, `cdev` is the structure to initialize, and `fops` the `file_operations` instance for this device, making it ready to add to the system. In `cdev_add()`, `p` is the `cdev` structure for the device, `dev` is the first device number for which this device is responsible (obtained dynamically), and `count` is the number of consecutive minor numbers corresponding to this device. When it succeeds, `cdev_add()` returns `0`, or else it returns a negative error code.

- The respective reverse operation of `cdev_add()` is `cdev_del()`, which removes the character device from the system and has the following prototype:
```c
void cdev_del(struct cdev *);
```

- At this step, the device is part of the system but not physically present. In other words, it is not visible in /dev yet. For the node to be created, you must use `device_create()`:
```c
struct device * device_create(struct class *class,
							  struct device *parent, dev_t devt,
							  void *drvdata, const char *fmt, ...)
```
This method creates a device and registers it with [[sysfs]]. In its argument, `class` is a pointer to `struct class` that this device should be registered to, `parent` is a pointer to the struct device parent of this new device if there is any, `devt` is the device number for the char device to be added, and `drvdata` is the data to be added to the device for callbacks.

- Because a device needs an existing class before being created, you must either create a class or use an existing one.
```c
struct class * class_create(struct module * owner, const char * name);
```

- Finally, `device_destroy()` will remove a device node from `/dev`, `cdev_del()` will make the system forget about this character device, `class_ unregister()` and `class_destroy()` will deregister and remove the class from the system, and finally, `unregister_chrdev_region()` will release our device number.