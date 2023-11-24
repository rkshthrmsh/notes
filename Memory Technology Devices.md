---
tags:
  - flashmem
  - embeddedLinux
---
#### Memory Technology Devices
MTD consists of three layers: a core set of functions, a set of drivers for various types of chips, and user-level drivers that present the flash memory as a character device or a block device, as shown in the following diagram:
![[Pasted image 20231003133334.png]]
The chip drivers are at the lowest level and interface with flash chips. Only a small number of drivers are needed for [[NOR Flash]] chips, enough to cover the CFI standard and variations. For [[NAND Flash]], you will need a driver for the NAND flash controller you are using; this is usually supplied as part of the board support package.

In most cases, you will want to partition the flash memory into a number of areas, for example, to provide space for a bootloader, a kernel image, or a root filesystem. In MTD, there are several ways to specify the size and location of partitions, with the main ones being as follows: 
- Through the kernel command line using CONFIG_MTD_CMDLINE_PARTS 
- Via the device tree using CONFIG_MTD_OF_PARTS 
- With a platform-mapping driver (deprecated in favor of device tree)
##### MTD Device Drivers
The upper level of the MTD subsystem contains a pair of device drivers: 
- A character device, with a major number of 90. There are two device nodes per MTD partition number, N: `/dev/mtdN` and `/dev/mtdNro`. The latter is just a read-only version of the former. The character devices are the most important: they allow you to access the underlying flash memory as an array of bytes so that you can read and write (program) the flash. It also implements a number of `ioctl` functions that allow you to erase blocks and manage the OOB area on NAND chips. There is a set of utility programs known as mtd-utils for manipulating flash memory that makes use of these ioctl functions. These include:
	- `flash_erase`: Erases a range of blocks.  
	- `flash_lock`: Locks a range of blocks. 
	- `flash_unlock`: Unlocks a range of blocks.  
	- `nanddump`: Dumps memory from NAND flash, optionally including the OOB area. Skips bad blocks. 
	- `nandtest`: Tests and performs diagnostics for NAND flash. 
	- `nandwrite`: Writes (programs) data from a file into NAND flash, skipping bad blocks.
- A block device, with a major number of 31 and a minor number of N. The device nodes are in the form `/dev/mtdblockN`. This is not used often because of severe limitations in handling bad blocks in NAND flash, no wear leveling, and lack of managing mismatch in size between filesystem blocks and flash erase blocks. It lacks a flash translation layer.