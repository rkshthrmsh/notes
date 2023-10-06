---
tags:
  - embeddedLinux
  - kernelModules
---
### Building a Linux Kernel Module
Two solutions exist for compiling a kernel module: 
- The first solution is when code is outside of the kernel source tree, which is known as **out-of-tree** building. Building a module this way does not allow integration into the kernel configuration/ compilation process, and the module needs to be built separately. It must be noted that with this solution, the module cannot be statically linked in the final kernel image – that is, it cannot be built in. Out-of-tree compilation only allows loadable kernel modules to be produced.
- The second solution is inside the kernel tree, which allows you to upstream your code, since it is well integrated into the kernel configuration/compilation process. This solution allows you to produce either a statically linked module (also known as built-in) or a loadable kernel module.
#### Kbuild for Linux Kernel Modules
[[Kernel#Compiling with Kbuild|Kbuild]] is the kernel's buildsystem. Using this tool, a kernel module build command pattern will look like:
```
make -C $KERNEL_SRC M=$(shell pwd) [target]
```
In the preceding pattern, `$KERNEL_SRC` refers to the path of the prebuilt kernel directory, `-C $KERNEL_SRC` instructs make to change into the specified directory when executing and change back when finished, and `M=$(shell pwd)` instructs the kernel build system to move back into this directory to find the module that is being built. `[target]` corresponds to the subset of the make targets available when building an external module. These are as follows: 
- `modules`: This is the default target for external modules. It has the same functionality as if no target was specified. 
- `modules_install`: This installs the external module(s). The default location is `/ lib/modules/<kernel_release>/extra/`. This path can be overridden. 
- `clean`: This removes all generated files (in the module directory only).

We must also specify the name of the module(s) to be built, along with the list of requisite source files. It can be as simple as the following single line:
```
obj-$(CONFIG_XXX) := <module_name>.o
```
In the preceding, the kernel build system will build `<module_name>.o` from `<module_name>.c` or `<module_name>.S`, and after linking, it will result in the `<module_name>.ko` kernel loadable module or will be part of the single-file kernel image. `$(CONFIG_XXX)` can evaluate to either `y`, `m`, or left blank:
- `m`: The `obj-m` variable is used and `<module_name>.o` will be built as a loadable kernel module.
- `y`: The `obj-y` variable is used and `<module_name>.o` will be built as part of the kernel.
- If nothing is set: The `obj-` variable is used and no module will be built.

When the module is built from multiple source files, an additional line is needed for listing these source files, as shown here:
```
<module_name>-y := <file1>.o <file2>.o
```

However, if you wanted to build two modules, let's say `foo.ko` and `bar.ko`, the Makefile line would be as follows:
```
obj-m := foo.o bar.o
```

If `foo.o` and `bar.o` are made of source files other than `foo.c` and `bar.c`, you can specify the appropriate source files of each object file, as shown here:
```
obj-m := foo.o bar.o
foo-y := foo1.o foo2.o ...
bar-y := bar1.o bar2.o bar3.o...
```
#### Out-of-Tree Building
A complete and precompiled kernel source tree is needed before building an external module. Then, there are two ways to obtain a prebuilt kernel version:
- Installing the `linux-headers-*` package from the distribution package feed. 
  NOTE: This applies only for x86 native compilations unless your embedded target runs a Linux distribution that maintains a package feed (such as Raspbian).
- Building it using build systems such as [[The Yocto Project|Yocto]] and [[Build Systems#Buildroot|Buildroot]].
##### Compiling Natively Using Linux Headers
The Linux headers can be installed with the following command:
```
sudo apt update
sudoo apt install linux-headers-$(uname -r)
```
This will install preconfigured and prebuilt kernel headers (not the whole source tree) in `/usr/src/linux-headers-$(uname -r)`. There will be a symbolic link, `/lib/ modules/$(uname -r)/build`, pointing to the previously installed headers. It is the path you should specify as the kernel directory in Makefile.
##### Cross-Compiling Out-of-Tree Modules
When it comes to cross-compiling an out-of-tree kernel module, there are essentially two variables that the kernel make command needs to be aware of. These are `ARCH` and `CROSS_COMPILE`, which respectively represent the target architecture and the crosscompiler prefix. Moreover, the location of a prebuilt kernel for the target architecture must be specified in the makefile. In our skeleton, we called it `KERNEL_SRC`.

What changes between native compilation and cross-compilation of an out-of-tree kernel module is the final `make` command, which looks like the following for a 32-bit Arm architecture:
```
make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf-
```
#### In-Tree Building
With in-tree builds, the driver source codes are usually placed in the appropriate subdirectory under `drivers/` (for example, `drivers/char` for character drivers). Every subdirectory in `drivers` has bot a `Makefile` and `Kconfig` files. The `Kconfig` and the `Makefile` file should be modified to include the new driver. For example:
```
# Kconfig
config PACKT_MYCDEV
	tristate "Our packtpub special character driver"
	default m
	help
		Say Y here to support /dev/mycdev char device.
		The /dev/mycdev is used to access packtpub.
```

```
# Makefile
obj-$(CONFIG_PACKT_MYCDEV) += mychardev.o
```