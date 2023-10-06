---
tags:
  - embeddedLinux
  - deviceDrivers
---
#### `sysfs`
`sysfs` is a represenation of kernel objects, attributes, and relationships. A kernel object is represented as a directory, an attribute is a file, and a relationship is a symbolic link. Practically,  looking in `/sys` provides the kernel's view of the system.
##### The Devices: `/sys/devices`
This is the kernel's view of the devices that have been discovered since boot and how they are connected to each other. It is organized at the top level by the system bus, so what you see varies from one system to another. There are three subdirectories present on all systems:
- `system/`:  This contains devices at the heart of the system, including CPUs and clocks.
- `virtual/`: This contains devices that are memory-based. You will find the memory devices that appear as `/dev/null`, `/dev/random`, and `/dev/zero in virtual/mem`. You will find the loopback device, `lo`, in `virtual/net`.
- `platform/`: This is a catch-all for devices that are not connected via a conventional hardware bus. This may be almost everything on an embedded device.
##### The Drivers: `/sys/class`
This is a view of the device drivers presented by their type. In other words, it is a software view rather than a hardware view. Each of the subdirectories represents a class of driver and is implemented by a component of the driver framework. For example, UART devices are managed by the `tty` layer, and you will find them in `/sys/class/tty`.

Within each directory, there are links and files. The link called `device` points to the hardware object for the device. The link called `subsystem` points back to the parent subsystem, `/sys/class/tty`. The remaining directory entries are attributes. Some are specific to a serial port, such as `xmit_fifo_size`, while others apply to many types of device, such as the interrupt number, `irq`, and the device number, `dev`. Some attribute files are writable and allow you to tune parameters in the driver at runtime. 

The `dev` attribute is particularly interesting. If you look at its value, you will find the major and minor numbers of the device.This attribute is created when the driver registers this interface. It is from this file that `udev` and `mdev` find the major and minor numbers of the device driver.
##### The Block Drivers: `/sys/block`
This contains a subdirectory for each block device.