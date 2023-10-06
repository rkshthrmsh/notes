---
tags:
  - embeddedLinux
---
## Build Systems
Build systems help simplify embedded Linux development by automating as many steps as possible in the process. A good build system is one that is able to:
- Download source code from upstream and cache it locally.
- Apply patches to enable cross compilation, apply local configuration policies, fix bugs, and so on.
- Build the various components.
- Create a staging area and assemble the root filesystem.
- Create image files in various formats as necessary for loading onto the target.
- Add custom packages containing applications or kernel changes.
- Select various root filesystem profiles: small or large, graphics or not, etc.
- Create a standalone SDK that can be distributed to other developers so that they don't have to install the complete build system.
- Track open source licenses used by the various selected packages.
- Have a user friendly interface.

Commonly available build systems are:
- Buildroot
- EmbToolkit
- OpenEmbedded
- OpenWrt
- PTXdist
- The Yocto Project

### Buildroot
Buildroot was primarily developed to automate the process of building root filesystem images. However, it can also build toolchains, bootloaders, and kernel images. It uses the kernel Kconfig/Kbuild mechanism. So, it can be build from scratch using the `make menuconfig` command (`xconfig` or `gconfig` also work) or by choosing one of the many configurations available in the `configs/` directory (`make list-defconfigs` lists all the default configurations).

A default configuration can be built as follows:
```
make <board/arch>_defconfig
make
```
The output of the build will contain two directories:
- `dl/`: Archives of the upstream projects that Buildroot has built.
- `output/`: All the intermediate and final compiled resources
	- `build/`: Build directory for each component.
	- `host/`: Various tools required by Buildroot that run on the host, including the executables of the toolchain (`output/host/usr/bin`).
	- `images/`: Results of the build—bootloader, kernel, and root filesystem(s).
	- `staging/`: Symbolic link to the `sysroot` of the toolchain. Not to be confused with the staging area for a root filesystem.
	- `target/`: Staging area for the `root` directory. However, it cannot directly be used as a root filesystem because file ownership and permissions are not set correctly.

Buildroot can be used to create Board Support Packages (BSPs) for custom boards and to add custom code as a package into a build. More details on the process and steps involved are available in: *Mastering Embedded Linux Programming: Create Fast and Reliable Embedded Solutions with Linux 5.4 and the Yocto Project 3.1 (Dunfell)*
### [[The Yocto Project]]