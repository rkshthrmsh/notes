---
tags:
  - embeddedLinux
---
### Device Nodes
According to the Unix philosophy, *everything is a file*. Therefore, most devices in Linux are represented by device nodes (except network interfaces, which are sockets). Device nodes may be block devices—which are mass storage devices, like SD cards or hard disks—or character devices which cover everything else with the exception of network interfaces. 

The conventional location for device nodes is `/dev`. Device nodes can be created using a program called `mknod` as:
```
mknod <name> <type> <major> <minor>
```
The parameters are:
- `name`: Name of the device node to be created.
- `type`: `c` for character devices or `b` for block devices.
- `major` and `minor`: A pari of numbers used by the kernel to route file requests to appropriate device driver code.

A really minimal root filesystem needs just two nodes: `console` and `null`. The `console` needs to be accessible only by root, whereas `null` should be readable and writable by everyone.

Creating device nodes using `mknod` is hard and inflexible. Other ways of creating device nodes are:
- `devtempfs`: This is a pseudo filesystem that can be mounted over `/dev` at boot time. The kernel populates it with device nodes for all the devices the kernel currently knows about and for devices that are detected at runtime.
- `mdev`: A BusyBox applet used t populate a directory with devices and add new nodes as needed. The rules of ownership and modes of the nodes are handled by the `/etc/mdev.conf` configuration file.
- `udev`: Mainstream equivalent of `mdev` which is found on desktop Linux and in some embedded devices. Now a part of `systemd`
However, it must be noted that these methods take up additional boot time. If a fast boot time is of priority, then static device nodes are a better approach.