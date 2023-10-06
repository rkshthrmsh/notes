---
tags:
  - embeddedLinux
---
### U-Boot
U-Boot is an open source bootloader which supports a good number of processor architectures and a large number of individual boards and devices. It is hosted and maintained by Denx Software Engineering.
#### Building U-Boot
The git repository of u-boot contains several configuration files in the `config/` directory. Choosing the config which is appropriate for the target device is the first step in building U-Boot. Once the appropriate config is chosen, the `CROSS_COMPILE` make variable needs to be set to the prefix of the chosen cross compiler. Then, execute
```
make distclean
make <board_defconfig>
make
```

A successful compilation results in the following files:
- `u-boot`: U-Boot in elf format suitable for debugging
- `u-boot.map`: The symbol table
- `u-boot.bin`: U-Boot in raw binary format, suitable for running on your device
- `u-boot.img`: `u-boot.bin` with a U-boot header added, suitable for uploading to a running copy of U-Boot
- `u-boot.srec`: U-Boot in Motorola S-record format, suitable for transferring over a serial connection
- (Optionally) Secondary Program Loader (SPL) `MLO` is also built at the same time.
#### Installing U-Boot
Typically, SoCs contain a boot ROM that can be used to read boot code from various external sources such as SD cards, serial interfaces, or USB mass storage. When using an SD card, the SD card needs to be formatted to contain two partitions: a small (~64 MiB) formatted as `FAT32` which will contain the bootloader, and a larger one (1+ GiB) formatted as `ext4` for the root filesystem. With the partitions in place, `MLO` and `u-boot.img` need to be copied into the boot partition. Now, inserting the SD card into the target board and turning on the device as per specification for bootloader will give the U-Boot prompt.
#### Using U-Boot
The interface to U-Boot is usually through a command-line over a serial port. Useful commands include:
- `nand read 82000000 400000 200000`: This will read 0x200000 bytes from offset 0x400000 from the start of the NAND flash into RAM address 0x82000000.
- `setenv <var> <value>`
- `setenv <var>` to delete a variable
Environment variables can be stored in a file named `uboot.env` in a partition of the disk. As such, environment variables like `bootcmd` and `bootdelay` can be used to automate the boot process.
##### Creating Image
The `mkimage` package / script can be used to prepare files for U-Boot. U-Boot lacks a filesystem. Instead, blocks of information are tagged with a 64-byte header. An example usage of the command to prepare a kernel image for an Arm processor is:
`mkimage -A arm -O Linux -T kernel -C gzip -a 0x80008000 -e 0x80008000 -n 'Linux' -d zImage uImage`
##### Loading Images
While in the U-Boot command-line, a typical sequence used to load an image into memory is:
```
mmc rescan
fatload mmc 0:1 82000000 uimage
iminfo 82000000
```

`mmc rescan` reinitializes the MMC driver to detect the SD card. `fatload` reads a file from a FAT-formatted partition on the SD card, and the `iminfo` command provides information about the image at the specified address.

Images can also be loaded over a network using the Trivial File Transfer Protocol (TFTP) for faster iteration. This can be used to program images into the NAND flash memory and read them back using the `nand write` and `nand read` commands, respectively.
##### Booting Linux
Once the kernel has been loaded into RAM, it is ready to boot. The `bootm` command starts running a kernel image. The general command looks like:
`bootm [address of kernel] [address of ramdisk(initramfs)] [address of dtb]`

This command can be automated by setting the U-boot environment variables `bootcmd`, to contain the boot command, and `bootdelay` to run the boot commands after a delay (in seconds).

##### Falcon Mode
U-Boot provides a falcon mode to reduce the number of phases involved in the booting process. In falcon mode, the SPL loads a kernel image directly—there is no user interaction nor any scripts involved in the process.