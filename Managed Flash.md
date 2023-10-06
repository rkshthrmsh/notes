---
tags:
  - embeddedLinux
  - flashmem
---
##### Managed Flash
Managed flash memory consists of one or more NAND flash chips, packaged with a controller that handles the complexities of flash memory and presents a hardware interface similar to that of a hard disk. The attraction is that it removes complexity from the driver software and insulates the system designer from the frequent changes in flash technology. SD cards, eMMC chips, and USB flash drives fit into this category.
###### [[MultiMediaCard and Secure Digital Cards]]
###### eMMC
Embedded MMC or eMMC is simply [[MultiMediaCard and Secure Digital Cards|MMC]] memory that's been packaged so that it can be soldered on to the motherboard, using a 4- or 8-bit interface for data transfer. However, they are intended to be used as storage for an operating system, so the components are capable of performing that task. The chips are usually not preformatted with any filesystem.
###### USB Flash Drive
In this case, memory is accessed through a USB interface and the controller implements the USB mass storage specification, as well as the flash translation layer and interface to the flash chip, or chips. The USB mass storage protocol, in turn, is based on the SCSI disk command set. As with [[MultiMediaCard and Secure Digital Cards|MMC and SD cards]], they are usually preformatted with a FAT filesystem.
###### Universal Flash Storage
Like eMMC, it is packaged in a chip that is mounted on the motherboard. It has a high-speed serial interface and can achieve data rates greater than eMMC. It supports a SCSI disk command set.