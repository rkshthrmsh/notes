---
tags:
  - embeddedLinux
---
## Root Filesystem
The kernel receives a root filesystem either as an `initramfs` passed as a pointer from the bootloader, or by mounting the block device using the `root=` parameter on the kernel command line. Upon receiving a root filesystem, the kernel will execute the first program called `init`.

A minimal root filesystem contains:
- `init`: The first program run by the kernel, which starts off everything.
- `Shell`: To receive the command prompt and to run scripts called by `init` and other programs.
- `Daemons`: A daemon is a background program that provides service to others. Examples include `syslogd` (system log daemon) and `sshd` (secure shell daemon). The `init` program starts the initial population of daemons to support the main system applications. In fact, `init` itself is a daemon that provides the service of launching other daemons.
- `Shared libraries`: For programs that are linked with shared libraries.
- `Configuration files`: Configuration for `init` and other daemons. Usually stored in the `/etc` directory.
- `Device nodes`: Special files that give access to various device drivers.
- `proc` and `sys`: Two pseudo filesystems that represent kernel data structures as a hierarchy of directories and files.
- `Kernel modules`: These need to be installed in the root filesystem, usually in `/lib/modules/<kernel version>`
### Directory Layout
Beyond the existence of the `init` program, the Linux kernel does not care about the layout of files and directories. However, many program expect certain files to be in certain places. The Filesystem Hierarchy Standard (FHS) defines a basic layout for most Linux systems:
- `/bin`: Programs essential for all users.
- `/dev`: Device nodes and other special files.
- `/etc`: System configuration files.
- `/lib`: Essential shared libraries.
- `/proc`: Information about processes represented as virtual files.
- `/sbin`: Program essential to the system administrator.
- `/sys`: Information about devices and their drivers represented as virtual files.
- `/tmp`: A place to put temporary or volatile files.
- `/usr`: Additional programs, libraries, and system administrator utilities.
- `/var`: Hierarchy of files and directories that maybe modified at runtime.
### The Staging Directory
The first step in the process of creating a root filesystem is to create a staging directory with the same directory layout as above. Care should be taken to ensure that sensitive resources in this staging area can only be accessed by the `root` user when the root filesystem is added to the target. Moreover, programs should be run using non-root privileges so that if they are compromised by an outside attack, access to system resources is minimized.

With this in mind, the staging directory can be populated with an `init` program, a shell (`bash`, `ash`, `hush`, etc.), and utilities. BusyBox and ToyBox are two projects that can help in building these programs.

Next up are the libraries for the root filesystem. In order to create the smallest memory footprint for the libraries, one can cherrypick libraries from `sysroot` by parsing the dependencies using the `readelf` command and grep-ing for `program interpreter` and `Shared library`. Care should be taken to include all the symbolic links as well. Another method to reduce the size of libraries is by stripping them of symbol tables since debugging is not necessary in a production system.
### [[Device Nodes]]
### The `proc` and `sysfs` Filesystems
`proc` and `sysfs` are two pseudo filesystems that give a window into the inner workings of the kernel. They both expose kernel data as files in a hierarchy of directories, which are formatted on the fly by a function in the kernel. Some files are also writable, and when used with the right format and privileges, will modify values stored in the kernel's memory. Together, they provide an interface to the kernel.

`proc` exposes information about processes to the user space. Each process is represented as a directory `/proc/<PID>`. It is this directory that the `ps` command reads to generate its output. In addition, files like `/proc/cpuinfo` and `/proc/interrupts` contain information about the CPU and interrupts, respectively. Finally, `proc/sys` contains files that display and control the state and behavior of kernel subsystems like scheduling, memory management, and networking.

On the other hand `sysfs` presents the kernel driver model to the user space. It exports a hierarchy of file related to drivers and device drivers and the way they are interconnected.

File systems are mounted using the `mount` command:
```
mount [-t vfstype] [-o options] device directory
```

In the absence of `device` for these pseudo filesystems, it is common practice to use the filesystem type in place of the device.
### Transferring the Root Filesystem to the Target
There are three ways of transferring the root filesystem to the target:
- **initramfs**: Also called ramdisk, this is a filesystem image loaded onto the RAM by the bootloader. Advantages of the ramdisk are that they are easy to create, have no dependency on mass storage devices, and can be used as a fallback maintenance mode when the main root filesystem is updating. However, since the contents of the root filesystem are dynamic, any changes made to a ramdisk at runtime are lost unless some configuration parameters are stored in a non-volatile memory.
- **Disk image**: In this version, the root filesystem is formatted and loaded using a mass storage device such as an SD card or a flash memory.
- **Network filesystem**: The staging directory in the development machine can be exported over a network and mounted by the target at boot time. This is often done during a development phase to iterate rapidly.
#### `initramfs`
An `initramfs` is a compressed `cpio` archive. `cpio` is an old Unix archive format similar to TAR and ZIP, but easier to decode. Therefore, it requires lesser code in the kernel. There are three different ways to create a boot ramdisk:
- As a standalone `cpio` archive: This option gives the most flexibility because the kernel and ramdisk can be mixed and matched.
- As a `cpio` archive embedded in the kernel image: Some bootloaders are unable to handle two images (kernel and root filesystem) separately. In such situations, the ramdisk archive needs to be embedded with the kernel image.
- As a device table that the kernel build system processes as part of the build: A device table is a text file that lists the directories, nodes, symbolic links, and files that go into an archive or filesystem image. Since it is a simple text file, no `root` permissions are required to create it. The syntax is fairly simple, and the kernel source code even provides a bash script (`usr/gen_initramfs_list.sh`) to convert the root filesystem staging area into a device table and change the permissions as necessary for the target.
### The `init` program
[[Starting Up—The init Program|init]] is the first program run by a Unix system once it has booted-up. The `init` program begins by reading the configuration file `/etc/inittab`. One of the first tasks executed by the init program is running the shell script `rcS` located in `/etc/init.d`. All initialization commands that need to be performed at boot need to be added to the `rcS` bash script.
#### Starting Daemon Processes
Typically, one would need to run background processes at startup. This can be done by adding a line to the `/etc/inittab` configuration file.

An example of `inittab` is:
```
::sysinit:/etc/init.d/rcS
::askfirst:-/bin/ash
::respawn:/sbin/syslogd -n
```

An example of the `rcS` mounting `/proc` and `/sysfs`:
```
#!/bin/sh
mount -t proc proc /proc
mount -t sysfs sysfs /sysfs
```
### Configuring User Accounts
New users are configured in the `/etc/passwd` file. This file uses one line per user with seven fields of information separated by a colon, which are:
- Login name
- Hash code used to verify the password. Usually, this is replaced by an `x` to indicate that the password is stored in `/etc/shadow`. This is because the `passwd` file needs to be world-readable in order for various programs to access username information. Having the hashes stored here, then, makes it possible for hackers to reverse engineer the password.
- User ID
- Group ID
- Comment field which is usually left blank
- User's `home` directory
- Shell for the user

Simple example:
```
root:x:0:0:root:/root:/bin/sh
daemon:x:1:1:daemon:/usr/sbin:/bin/false
```

In a similar manner, group names and information are stored in the `/etc/group` file.

After adding the `/etc/passwd`, `/etc/group`, and `/etc/shadow` files to the staging directory, the `inittab` file should be modified to add a program called `getty`, which initiates the login procedure. It is configured using the `respawn` keyword to ensure that it restarts everytime a console is terminated.
```
::sysinit:/etc/init.d/rcS
::respawn:/sbin/getty 115200 console
```

### Creating Filesystem Images
Filesystem images can be created by using a device table. There are multiple tools that can take a device table as an input and create the filesystem image These vary based on the output image format:
- jffs2: mkfs.jffsw
- ubifs: mkfs:ubifs
- ext2: genext2fs
### Mounting Root Filesystem Using NFS
During the development phase, it is often useful to mount the root filesystem using the network. This makes the debug and development iterations faster, since changes made to the root filesystem are available immediately to the target, and all the logs on the target are readily available to the host. In addition, the target device has access to the larger amount of storage available on the development device.

The first step in this process is to install and configure an NFS server on the host. This can be done using the `nfs-kernel-server` package. Next, the NFS server needs to be told which directories to export to the network. This is controlled by the `/etc/exports`.
An example of this is:
```
/home/rkshthrmsh/rootfs *(rw, sync, no_subtree_check, no_root_squash)
```

After this, the target must be setup to mount the root filesystem over NFS. The `CONFIG_ROOT_NFS` variable needs to be configured in the kernel, and finally Linux can be configured to do the mount at boot time by adding this to the kernel command line:
```
root=/dev/nfs rw nfsroot=<host-ip>:<root-dir> ip=<target-ip>
```