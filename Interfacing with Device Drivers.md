---
tags:
  - embeddedLinux
  - deviceDrivers
---
## Interfacing with Device Drivers
Kernel device drivers are the mechanism through which the underlying hardware is exposed to the rest of the system. Kernel device driver code runs at a high privilege level. It has full access to the processor address space and hardware registers. It can handle interrupts and DMA transfers. It can also make use of the sophisticated kernel infrastructure for synchronization and memory management. However, there is a downside to this; if something goes wrong in a buggy driver, it can go really wrong and bring the system down. Consequently, there is a principle that device drivers should be as simple as possible by just providing information to applications where the real decisions are made. You often hear this being expressed as *no policy in the kernel*.

In Linux there are three main types of device drivers:
- **Character**: This is for an unbuffered I/O with a rich range of functions and a thin layer between the application code and the driver. It is the first choice when implementing custom device drivers.
- **Block**: This has an interface tailored for block I/O to and from mass storage devices. There is a thick layer of buffering designed to make disk reads and writes as fast as possible, which makes it unsuitable for anything else.
- **Network**: This is similar to a block device but is used for transmitting and receiving network packets rather than disk blocks.
### Character Devices
Character devices are identified in the user space by a special file called a **device node**. This filename is mapped to a device driver using the major and minor numbers associated with it. Broadly speaking, the **major number** maps the device node to a particular device driver, while the **minor number** tells the driver which interface is being accessed. The major number is 12 bits long, and the minor number is 20 bits. When you open a character device node, the kernel checks whether the major and minor numbers fall into a range registered by a character device driver. If so, it passes the call to the driver; otherwise, the open call fails.

Device nodes can be created in several ways: 
- `devtmpfs`: The device node is created when the device driver registers a new device interface using a base name supplied by the driver and an instance number. 
- `udev` or `mdev` (without `devtmpfs`): Essentially the same as with `devtmpfs`, except that a user space daemon program has to extract the device name from `sysfs` and create the node. 
- `mknod`: If you are using static device nodes, they are created manually using `mknod`.
### Block Devices
Block devices are also associated with a device node, which also has major and minor numbers. With block devices, the major number is used to identify the device driver and the minor number is used to identify the partition.

A user space program can open and interact with a block device directly via the device node. This is not a common thing to do, though, and is usually only done to perform administrative operations such as creating partitions, formatting a partition with a filesystem, and mounting. Once the filesystem has been mounted, you interact with the block device indirectly through the files in that filesystem.
### Network Devices
Network devices are not accessed through device nodes, and they do not have major and minor numbers. Instead, a network device is allocated a name by the kernel, based on a string and an instance number.

Usually, the network interface name is only used when configuring the network using utilities, such as ip and ifconfig, to establish a network address and route. Thereafter, you interact with the network driver indirectly by opening sockets and letting the network layer decide how to route them to the right interface. However, it is possible to access network devices directly from the user space by creating a socket and using the `ioctl` commands listed in include`/linux/sockios.h`.
### Finding Out about Drivers at Runtime
- `/proc/devices` lists the character and block device drivers that are currently loaded and active. However, this does not indicate how many devices each driver is attached to.
- For network drivers, use tools such as `ifconfig` or `ip`.
- To learn more about devices attached to USB or PCI, use `lsusb` and `lspci` commands.
#### [[sysfs]]
### Device Drivers in User Space
There are generic device drivers for many common types of devices that allow you to interact with hardware directly from user space, without having to write a line of kernel code. These drivers fall into two broad categories: those that you control through files in `sysfs`, including GPIO and LEDs, and serial buses that expose a generic interface through a device node, such as I2C.
#### [[GPIO]]
#### [[LEDs]]
#### [[I2C]]
#### [[SPI]]
### [[Writing a Kernel Device Driver]]
### [[Discovering the Hardware Configuration]]