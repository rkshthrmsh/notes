---
tags:
  - embeddedLinux
---
## Kernel
Kernel is the component of embedded Linux that is responsible for managing resources, interfacing with hardware, and providing an API that offers a useful level of abstraction to user space programs. 

User space applications run with a low CPU privilege and primarily use the C library to interface with the kernel space. The system call interface uses architecture specific methods, such as traps or interrupts, to switch the CPU from low-privilege user mode to high-privilege kernel mode.

The system call handler dispatches calls to the appropriate kernel subsystem: memory allocations to the memory manager, filesystem calls to filesystem code, etc.

![[Pasted image 20230928102757.png]]

Kernel is just one part of an OS. Creating a complete OS requires the kernel to be combined with a user space such as the GNU user space or the Android user space. Being decoupled from a fixed user space gives Linux kernel the flexibility in choosing init systems(`runit` vs. `systemd`), C libraries (`musl` vs. `glibc`), and package formats (`.apk` vs. `.deb`).

### Choosing a Kernel
#### Kernel Development Cycle
Linux is developed at a fast pace—a new version is released every 8–12 weeks. A full development cycle begins with a merge window of 2 weeks,  during which Linus Torvalds accepts patches for new features. This is followed by a stabilization phase, during which release candidates are produced weekly with version numbers `-rc<n>`. People test the release candidates and submit bug reports and fixes. Once all significant bugs are fixed, the kernel is released with a two-field version number (ex.: `5.4`).
#### Stable and Long-Term Releases
Once the kernel is released on the mainline, it is moved to the stable tree, where it is maintained and any new bugs are fixed. In the meantime, the mainline kernel begins the next development cycle. Releases of the stable kernel are marked by a third number (ex.: `5.4.9`). The stable release is typically only update until the next mainline release. Therefore, only one or two stable kernels are listed on the official website.

Since the development happens at a fast pace, there maybe users who would like extended update periods. For this reason, long-term releases are maintained for 2 years or more. There is at least one long term kernel release each year.
### Building the Kernel
#### Kernel Repo Structure
The latest stable or long-term release can be downloaded from the official website as a tarball. Since the project is quite large, it is helpful to understand the main directories:
- arch: Contains architecture-specific files. There is one subdirectory per architecture.
- Documentation: Contains kernel documentation. Always look here first if you want to find more information about an aspect of Linux. 
- drivers: Contains device drivers – thousands of them. There is a subdirectory for each type of driver. 
- fs: Contains filesystem code. 
- include: Contains kernel header files, including those required when building the toolchain. 
- init: Contains the kernel startup code. 
- kernel: Contains core functions, including scheduling, locking, timers, power management, and debug/trace code. 
- mm: Contains memory management. 
- net: Contains network protocols. 
- scripts: Contains many useful scripts, including the device tree compiler (DTC). 
- tools: Contains many useful tools, including the Linux performance counters tool, perf.

#### Kconfig: Kernel Configuration
One of the strengths of Linux is its high degree of configurability—the kernel can be configured for a wider range of applications. Naturally, there are thousands of configuration options, and all of these are managed by the `Kconfig` configuration mechanism and the `Kbuild` build system. `Kconfig/Kbuild` are used in a number of other projects as well, such as crosstool-NG, U-Boot, Barebox, and BusyBox.

A hierarchy of  `Kconfig` files is used to declare the configuration option in specialized `kconfig-language` syntax. Each `Kconfig` file consists largely of menus, delineated by the `menu` and `endmenu` keywords, and menu items marked by the `config` keyword. Important keywords with a `config` code block are:
- `bool`, `tristate`, `int`, `hex`, `string`: Represent data type of the config item.
- `depends`: Indicates a dependancy for the config item. The config item is only shown in the menu if all its dependencies are enabled.
- `select`: Represents reverse dependency—enabling the config item automatically enables all the reverse dependencies indicated by this keyword.

A configuration utility such as `menuconfig`, `xconfig`, or `gconfig` is required to read the `Kconfig` files and produce a `.config` file. Example of using `menuconfig`:
```
make ARCH=arm menuconfig
```

To make the process of navigating thousands of configuration options easier, the `arch/$ARCH/configs` directory contains working configuration files for a single SoC or a group of SoCs, which can be used directly to create the `.config`
```
make ARCH=arm <arch>_defconfig
```

Another useful target is `oldconfig`, which helps in moving a configuration to a newer kernel version.
```
make ARCH=arm oldconfig
```

#### Kernel Modules
A kernel module is a piece of code that is dynamically linked with the kernel at runtime, thereby extending the functionality of the kernel. Kernel modules are typically used in desktop Linux distributions to load the correct device and kernel function at runtime. This reduces the need to statically link every single driver into the kernel, which makes it infeasibly large.

Kernel modules are a good idea when:
- The module is proprietary, since kernel modules are a gray area as per the GPL2 license.
- Reducing boot time, by only loading essential drivers.
- Reducing size of the kernel.

### Compiling with Kbuild
`Kbuild` is the kernel build system, essentially a set of make scripts that refer to the `.config` file, work out dependencies, and compile everything necessary to produce a kernel image. The kernel image will contain all the statically linked components, and optionally a device tree and one or more kernel modules.

The first step in building a kernel image is identifying the file bootloader expects. This target could be `uImage` (for U-boot), `zImage`, or `bzImage`. A kernel build generates two artifacts in the top directory: `vmlinux` which is the kernel as an ELF binary, and `System.map` with the symbol table in a human-readable form. Most bootloaders cannot handle ELF directly, so there is further stage to convert `vmlinux` into the appropriate format.

The next step is to build the device tree. This is done using the `dtbs` target according to the rules in he `arch/$ARCH/boot/dts/Makefile`. The compiled output is a `.dtb` device tree binary.

Kernel modules are compiled in the next stage using the `modules` target. The compiled modules need to be installed in the right location, which can be done by passing the path using `INSTALL_MOD_PATH` variable to the `modules_install` target.

### Booting the Kernel
In order to transition to user space, the kernel has to mount a root filesystem and execute a program in that root filesystem. This can be achieved by a ramdisk or by mounting a real filesystem on a block device. The `init/main.c` file contains the code for this. In particular, the `rest_init()` function creates the first thread and runs the code in `kernel_init()`.

Kernel messages can be printed using the `printk()` function. The messages are categorized according to importance, with `0` being the highest.
![[Pasted image 20230928140750.png]]
### Using TFTP to Load the Kernel
Loading the kernel and the device tree over the network along with the ability to [[Root Filesystem#Mounting Root Filesystem Using NFS|mount the root filesystem over NFS]] helps with faster iteration during the development phase. This can be achieved using the Trivial File Transfer Protocol (TFTP). The only thing remaining then is the bootloader, which can be built and deployed to the target using an SD Card.

First, a TFTP daemon needs to be installed using the Ubuntu package `tftpd-hpa`. By default, `tftpd-hpa` grants read-only access to files in the `/var/lib/tftpboot` directory. So, with the TFTP daemon running, one can copy the kernel image and device tree binary files to this location. Then the bootloader can be configured to boot the kernel over TFTP at the bootloader command prompt. The following is an example using U-Boot:
```
setenv serverip <server-ip>
setenv ipaddr <ipaddr>
tftpboot <memory> <kernel-image>
tftpboot <memory> <dtb>
setenv npath <path to rootfs staging directory>
setenv bootargs console=ttyO0,115200 root=.dev.nfs rw nfsroot=${serverip}:${npath} ip=${ipaddr}
bootz 0x80200000 - 0x80f00000
```