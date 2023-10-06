---
tags:
  - embeddedLinux
  - flashmem
---
###### MultiMediaCard and Secure Digital Cards
The hardware interface for MMC and SD cards is very similar, and it is possible to use full-sized MMC cards in full-sized SD card slots (but not the other way round). Early incarnations used a 1-bit Serial Peripheral Interface (SPI); more recent cards use a 4-bit interface.

There is a command set for reading and writing memory in sectors of 512 bytes. Inside the package is a microcontroller and one or more NAND flash chips, as shown in the following diagram:
![[Pasted image 20231003125817.png]]
The microcontroller implements the command set and manages the flash memory, performing the function of a flash translation layer. They are preformatted with a FAT filesystem: FAT16 on SDSC cards, FAT32 on SDHC, and exFAT on SDXC. It is questionable whether any of them are sufficiently reliable for deep embedded use, and certainly not with a FAT filesystem, which is prone to file corruption.