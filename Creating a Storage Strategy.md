---
tags:
  - embeddedLinux
---
## Creating a Storage Strategy
### Storage Options
Embedded devices need storage that takes little power and is physically compact, robust, and reliable over a lifetime of perhaps tens of years. In almost all cases, this means solidstate storage. Solid-state storage was introduced many years ago with read-only memory (ROM), but for the past 20 years, it has been a flash memory of some kind. There have been several generations of flash memory in that time, progressing from NOR to NAND to managed flash such as eMMC.
#### [[NOR Flash]]
#### [[NAND Flash]]
### Accessing Flash Memory from the Bootloader
[[U-Boot]] has drivers for [[NOR Flash]] CFI chips in `drivers/mtd` and utilizes various `erase` commands to erase memory and `cp.b` to copy data byte by byte, programming the flash cells.
```
=> tftpboot 100000 uImage
=> erase 40040000 403fffff
=> cp.b 100000 40040000 $(filesize)
```

For [[NAND Flash]], you need a driver for the NAND flash controller on your SoC, which you can find in the U-Boot source code in the `drivers/mtd/nand/` directory. You can use the `nand` command to manage memory using the erase, write, and read sub-commands.
```
=> tftpboot 82000000 uImage
=> nand erase 280000 400000
=> nand write 82000000 280000 $(filesize)
```

U-Boot has drivers for several [[MultiMediaCard and Secure Digital Cards|MMC]] controllers in `drivers/mmc`. You can access raw data using `mmc read` and `mmc write` at the user interface level, which allows you to handle raw kernel and filesystem images. U-Boot can also read files from the FAT32 and ext4 filesystems on MMC storage.
### Accessing Flash Memory from Linux
Raw NOR and NAND flash memory is handled by the **Memory Technology Device subsystem**, or **MTD**, which provides you with basic interfaces to read, erase, and write blocks of flash memory. In the case of NAND flash, there are also functions that handle the OOB area and are used to identify bad blocks. 

For managed flash, you need drivers to handle a particular hardware interface. MMC/SD cards and eMMC use the `mmcblk` driver, while CompactFlash and hard drives use the SCSI disk driver, `sd`. USB flash drives use the `usb_storage` driver, together with the `sd` driver.
#### [[Memory Technology Devices]]
#### MMC Block Driver
MMC/SD cards and eMMC chips are accessed using the `mmcblk` block driver. You need a host controller to match the MMC adapter you are using, which is part of the board support package.

MMC storage is partitioned using a partition table in exactly the same way you would for hard disks; that is, by using `fdisk` or a similar utility.
### [[Flash Translation Layer]]
### Filesystem Choices
In most cases, the storage requirements can be divided into three categories:
- **Permanent, read-write data**: Runtime configuration, network parameters, passwords, data logs, and user data
- **Permanent, read-only data**: Programs, libraries, and configurations files that are constant; for example, the root filesystem
- **Volatile data**: Temporary storage; for example, `/tmp`

The choices for read-write storage are as follows:
- NOR: [[UBIFS]] or [[JFFS2]] 
- NAND: [[UBIFS]], [[JFFS2]], or [[YAFFS2]] 
- eMMC: [[ext4]] or [[F2FS]]
For read-only storage, you can use any of these, mounted with the `ro` attribute. Additionally, if you want to save space, you could use [[SquashFS]]. Finally, for volatile storage, there is only one choice: [[tmpfs]].