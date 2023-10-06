---
tags:
  - embeddedLinux
  - deviceDrivers
---
### Writing a Kernel Device Driver
Character drivers are the most flexible and should cover 90% of all your needs; network drivers apply if you are working with a network interface and block drivers are for mass storage.
#### [[Designing a Character Driver Interface]]
#### [[Anatomy of a Character Device Driver]]
#### Compiling Kernel Modules
The device driver code (for example, the one in [[Anatomy of a Character Device Driver]]) can be compiled in two ways:
- Out of tree, which, via a simple makefile, can use the kernel build system to compile the device driver code into a [[Kernel#Kernel Modules|kernel module]].
```
LINUXDIR := $(HOME)/MELP/build/linux
obj-m := dummy.o
all:
	make ARCH=arm CROSS_COMPILE=arm-cortex_a8-linux-gnueabihf\
	-C $(LINUXDIR) M=$(shell pwd)
clean:
	make -C $(LINUXDIR) M=$(shell pwd) clean
```
- In the kernel source tree: Choose a directory appropriate to the type of driver, such as `drivers/char`, then edit the makefile in the directory to add a line to build the driver unconditionally as a module or as a builtin.
```
obj-m += dummy.o # module
obj-y += dummy.o # builtin
```
#### Loading Kernel Modules
You can load, unload, and list modules using the simple `insmod`, `lsmod`, and `rmmod` commands, respectively.

Another option is to place the module in a subdirectory in `/lib/modules/<kernel release>` and create a **modules dependency database** using the `depmod -a` command. 
```
$ depmod -a
$ ls /lib/modules/4.8.12-yocto-standard
kernel modules.alias modules.dep modules.symbols
```
The information in the `modules.*` files is used by the `modprobe` command to locate a module by name rather than its full path.