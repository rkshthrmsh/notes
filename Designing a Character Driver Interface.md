---
tags:
  - embeddedLinux
  - deviceDrivers
---
#### Designing a Character Driver Interface
The main character driver interface is based on a stream of bytes, as you would have with a serial port. However, many devices don't fit this description. Luckily, there are other ways to communicate with device drivers than just `read` and `write`:
- `ioctl`: The `ioctl` function allows you to pass two arguments to your driver, and these can have any meaning you like. By convention, the first argument is a command, which selects one of several functions in your driver, while the second is a pointer to a structure, which serves as a container for the input and output parameters. This is a blank canvas that allows you to design any program interface you like. However, `ioctl` is deprecated in the kernel, and you will find it hard to get any drivers with new uses of `ioctl` accepted upstream. The kernel maintainers dislike `ioctl` because it makes kernel code and application code too interdependent, and it is hard to keep both of them in step across kernel versions and architectures.
- `sysfs`: This is the preferred way to do things now. The advantages are that it is somewhat selfdocumenting, so long as you choose descriptive names for the files. It is also scriptable because the file's content is usually text strings. On the other hand, the requirement for each file to contain a single value makes it hard to achieve atomicity if you need to change more than one value at a time.
- `mmap`: You can get direct access to kernel buffers and hardware registers by mapping kernel memory into user space, thus bypassing the kernel. You may still need some kernel code to handle interrupts and DMA. There is a subsystem that encapsulates this idea, known as `uio`, which is short for user I/O.
- `sigio`: You can send a signal from a driver using the kernel function named `kill_fasync()` to notify applications of an event, such as input becoming ready or an interrupt being received. By convention, the signal called `SIGIO` is used, but it could be any. The main problem is that it is difficult to write reliable signal handlers in the user space, and so it remains a little-used facility.
- `debugfs`: This is another pseudo filesystem that represents kernel data as files and directories, similar to `proc` and `sysfs`. The main distinction is that `debugfs` must not contain information that is needed for the normal operation of the system; it is for debug and trace information only. It is mounted as `mount -t debugfs debug /sys/kernel/debug`.
- `proc`: The `proc` filesystem is deprecated for all new code unless it relates to processes, which was the original intended purpose for the filesystem. However, you can use `proc` to publish any information you choose.
- `netlink`: This is a socket protocol family. `AF_NETLINK` creates a socket that links the kernel space to the user space. It was originally created so that network tools could communicate with the Linux network code to access the routing tables and other details. It is also used by `udev` to pass events from the kernel to the `udev` daemon. It is very rarely used in general device drivers.

Clearly, there are many ways of designing character device drivers. The only universal rule is the *principle of least astonishment*. In other words, application writers who are using your driver should find that everything works in a logical way, without any quirks or oddities.