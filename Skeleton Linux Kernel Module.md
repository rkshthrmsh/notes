---
tags:
  - embeddedLinux
  - kernelModules
---
### Skeleton Linux Kernel Module
```c
#include <linux/module.h>
#include <linux/init.h>
static int __init helloworld_init(void) {
	pr_info("Hello world initialization!\n"); return 0;
}

static void __exit helloworld_exit(void) {
	pr_info("Hello world exit\n");
}

module_init(helloworld_init);
module_exit(helloworld_exit);

MODULE_LICENSE("GPL");
MODULE_AUTHOR("John Madieu <john.madieu@gmail.com>");
MODULE_DESCRIPTION("Linux kernel module skeleton");
```
The `module.h` header file is mandatory for all kernel modules, and `init.h` is needed for the `__init` and `__exit` macros. The `__init` keyword tells the linker to place the symbols (variables or functions) they prefix in a dedicated section in the resulting kernel object file. This applies only to built-in modules, not to loadable ones. The kernel will run the initialization function of the driver for the first time during its boot sequence. Since the driver cannot be unloaded, its initialization function will never be called again until the next reboot. It is the same for the `__exit` keyword and the exit method, whose corresponding code is omitted when the module is compiled statically into the kernel or when module unloading support is not enabled because, in both cases, the exit function is never called.

The minimal requirement of a kernel module is an initialization method. This is a must. If the module can be built as a loadable module, then the exit method must be provided as well. For this, all that needs to be done is to inform the kernel about which functions should be executed as an entry or exit point by passing them as parameters to the `module_init()` and `module_exit()` macros.

A kernel module uses its `.modinfo` section to store information about the module. Any `MODULE_*` macro will update the content of this section with the values passed as parameters. Some of these macros are `MODULE_DESCRIPTION()`, `MODULE_AUTHOR()`, and `MODULE_LICENSE()`. That said, the real underlying macro provided by the kernel to add an entry to the module information section is `MODULE_INFO(tag, info)`, which adds generic information of the `tag = "info"` form. This means a driver author can add any freeform information they want.