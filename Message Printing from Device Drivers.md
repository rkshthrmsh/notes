---
tags:
  - embeddedLinux
  - kernelModules
---
### Message Printing from Device Drivers
`printk()` is to the kernel what `printf()` is to the user space. Written messages can be displayed using the `dmesg` command. Depending on how important the message to print was, `printk()` allows choosing between eight log-level messages, defined in `include/linux/kern_levels.h`, along with their meaning.

Today, while `printk()` remains the low-level message printing API, the printk/ log-level pair has been encoded into clearly named helpers, which are recommended for use in new drivers. These are as follows:
- `pr_<level>(...)`: This is used in regular modules that are not device drivers.
- `dev_<level>(struct device *dev, ...)`: This is to be used in device drivers.
- `netdev_<level>(struct net_device *dev, ...)`: This is used in `netdev` drivers exclusively.
In all these helpers, `<level>` represents the log level encoded into a meaningful name.
![[Pasted image 20231005155517.png]]

Log levels work in a way that, whenever a message is printed, the kernel compares the message log level with the current console log level; if the former is higher (lower value) than the last, the message will be immediately printed to the console. Checking the log level can be done by:
```
$ cat /proc/sys/kernel/printk
4   4    4   7
^
|---------- current log level
```
To change current log level:
```
echo <level> > /proc/sys/kernel/printk
```

In addition, the module output messages can be prefixed with a custom string using the `pr_fmt` macro. Foe example:
```c
# For prefixing with module name
# define pr_fmt(fmt) KBUILD_MODNAME ": " fmt

# Prefixing with function name
# define pr_fmt(fmt) "%s: " fmt, __func__
```