---
tags:
  - embeddedLinux
---

# Elements of Embedded Linux
## The four elements of embedded Linux
- Toolchain: The compiler and other tools needed to create code for a target device.
- Bootloader: The program that initializes the board and loads the Linux kernel.
- Kernel: Heart of the Linux OS that manages system resources and interfacing with hardware.
- Root filesystem: Filesystem with libraries and programs that are run once the kernel has completed initialization.

A fifth element worth mentioning here is the collection of programs specific to the embedded application.

## [[Toolchain]]

## Bootloader
The bootloader has two primary functions: to initialize the system up to a basic level from which the kernel can be loaded. It is the first piece of code that is executed following a power-on or a reset. During these early stages, the device is in a minimal state—DRAM controller is yet to be setup; NAND flash controllers, MMC controllers, and other devices are still unavailable. Typically, the only resources available are a single CPU core, on-chip static memory, and the boot ROM. Subsequently, more of the system is bought into operation in several phases. The final act of the bootloader is to load the kernel into RAM and create an execution environment for it. The interface between the bootloader and the kernel during this act is interface specific. However, in each case, the bootloader has to pass a pointer to a structure containing information about the hardware configuration, and pass a pointer to the kernel command line.

A secondary function of the bootloader is to provide a maintenance mode for updating boot configurations, loading new boot images into memory, and maybe running diagnostics.