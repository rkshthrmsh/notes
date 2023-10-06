---
tags:
  - embeddedLinux
  - swupdates
---
### Types of Software Update Mechanism
#### Symmetric Image Update
In this scheme, there are two copies of the operating system, each comprising the Linux kernel, root filesystem, and system applications.
![[Pasted image 20231003160904.png]]
This scheme works as follows:
1. The bootloader has a flag that indicates which image it should load. Initially, the flag is set to A, so the bootloader loads OS image A.
2. To install an update, the updater application, which is part of the operating system, overwrites OS image B.
3. When complete, the updater changes the boot flag to B and reboots.
4. Now the bootloader will load the new operating system.
5. When a further update is installed, the updater overwrites image A and changes the boot flag to A, and so you ping-pong between the two copies.
6. If an update fails before the boot flag is changed, the bootloader continues to load the good operating system.
There are several open source projects that implement symmetric image update: Mender, SWUpdate, RAUC (Robust Auto-Update Controller), and fwup are some examples.

There are some drawbacks to this scheme:
- One is that by updating an entire filesystem image, the size of the update package is large, which can put a strain on the network infrastructure connecting the devices. This can be mitigated by sending only the filesystem blocks that have changed by performing a binary diff of the new filesystem with the previous version.
- A second drawback is the need to keep storage space for a redundant copy of the root filesystem and other components. If the root filesystem is the largest component, it comes close to doubling the amount of flash memory you need to fit.
#### Asymmetric Image Update
Storage requirement, as compared to Symmetric Image Update, can be reduce by keeping a minimal operating system purely for updating the main one.
![[Pasted image 20231003161352.png]]
Steps involved in an asymmetric image update are:
1. Set the boot flag to point to the recovery OS and reboot.
2. Once the recovery OS is running, it can stream updates to the main operating system image.
3. If the update is interrupted, the bootloader will again boot into the recovery OS, which can resume the update.
4. Only when the update is complete and verified will the recovery OS clear the boot flag and reboot again, this time loading the new main operating system. 
5. The fallback in the case of a correct but buggy update is to drop the system back into recovery mode, which can attempt remedial actions, possibly by requesting an earlier update version.

Open-source projects that implement this scheme are: SWUpdate and RAUC.

A major drawback of this scheme is that while the recovery OS is running, the device is not operational. Such a scheme also does not allow for updates of the recovery OS itself.
#### Atomic File Updates
In this approach, redundant copies of a root filesystem is present in multiple directories of a single filesystem. Then, `chroot(8)` command is used to choose one of them at boot time. This allows one directory tree to be updated while another is mounted as the root directory. Furthermore, rather than making copies of files that have not changed between versions of the root filesystem, you could use links. That would save a lot of disk space and reduce the amount of data to be downloaded in an update package.

libOSTree is the most popular implementation of this scheme. With libOSTree, the files are stored on the target in the `/ostree/repo/objects` directory. They are given names such that several versions of the same file can exist in the repository. Then, a given set of files are linked into a deployment directory, which has a name such as `/ostree/deploy/os/29ff9.../`. This is referred to as checking out, since it has some similarities to the way a branch is checked out of a [[Git Branching|Git repository]]. Each deploy directory contains the files that make up a root filesystem.