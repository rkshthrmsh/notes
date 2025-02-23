---
tags:
  - embeddedLinux
  - kernelModules
---
## Understanding Linux Kernel Module Basic Concepts
When building the Linux kernel, the resulting image is a single file made by the linking of all object files that correspond to features enabled in the configuration. As a result, all included features are therefore available as soon as the kernel starts, even if the filesystem is not yet ready or does not exist. These features are built-in, and the corresponding modules are called static modules. Such a module is available at any time in the kernel image and thus can't be unloaded, at the cost of extra size to the final kernel image.

Some features (such as device drivers, filesystems, and frameworks) can, however, be compiled as loadable modules. Such modules are separated from the final kernel image and are loaded on demand. These can be considered as plugins that can be loaded/ unloaded dynamically to add or remove features (at runtime) to the kernel. Because each module is stored as a separate file on the filesystem, using loadable modules requires access to a filesystem.
### [[Skeleton Linux Kernel Module]]
### [[Building a Linux Kernel Module]]
### Module Parameters
Similar to a user program, a kernel module can accept arguments from the command line. This allows us to dynamically change the behavior of the module according to the given parameters.
```c
# include <linux/moduleparam.h>
module_param(name, type, perm)
```
This macro contains the following elements:
- `name`: The name of the variable used as the parameter
- `type`: Parameter's type
- `perm`: File permissions for `/sys/module/<module>/parameters/<param>`. Some examples of the permission are: `S_IWUSR`, `S_IRGRP`, `S_IXUGO`.
	- `S_I` is a prefix.
	- `R`, `W`, and `X` are read, write, and execute, respectively.
	- `USR`, `GRP`, and `UGO` are user; group; and user, group, and others, respectively.

The `OR` operation (`|`) can be used to set multiple permissions. If `perm` is `0`, the file parameter in [[sysfs]] will not be created.

When using module parameters, `MODULE_PARM_DESC` can be used on a per-parameter basis to describe each of them. This macro will populate the module information section of each parameter's description.

The following is an example that brings all these concepts together:
```c
# include <linux/moduleparam.h>
[...]

static char *mystr = "hello";
static int myint = 1;
static int myarr[3] = {0, 1, 2};

module_param(mystr, charp, S_IRUGO);
module_param(myint, int, S_IRUGO);
module_param_array(myarr, int, NULL, S_IWUSR|S_IRUSR);

MODULE_PARAM_DESC(mystr, "this is my char pointer variable");
MODULE_PARAM_DESC(myint, "this is my int variable");
MODULE_PARAM_DESC(myarr, "this is my array of int");

static int foo()
{
	pr_infor("mystring is a string: %s\n", mystr);
	pr_info("Array elements: %d\t%d\t%d", myarr[0], myarr[1], myarr[2]);
	return myint;
}
```

To load the module with parameters:
```
$ insmod hellomodule-params.ko mystring="packtpub" myint=15 myarr=1,2,3
```

`modinfo` can be used to display a description of parameters supported by the module:
```
$ modinfo hellomodule-params.ko
```

It is also possible to find and edit the current values for the parameters of a loaded module from Sysfs in `/sys/module/<name>/parameters`. In that directory, there is one file per parameter, containing the parameter value.
### [[Error Handling in Linux Kernel Modules]]
### [[Message Printing from Device Drivers]]