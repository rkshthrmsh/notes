---
tags:
  - embeddedLinux
---
## Bootloader
The bootloader has two primary functions: to initialize the system up to a basic level from which the kernel can be loaded. It is the first piece of code that is executed following a power-on or a reset. During these early stages, the device is in a minimal state—DRAM controller is yet to be setup; NAND flash controllers, MMC controllers, and other devices are still unavailable. Typically, the only resources available are a single CPU core, on-chip static memory, and the boot ROM. Subsequently, more of the system is bought into operation in several phases. The final act of the bootloader is to load the kernel into RAM and create an execution environment for it. The interface between the bootloader and the kernel during this act is interface specific. However, in each case, the bootloader has to pass a pointer to a structure containing information about the hardware configuration, and pass a pointer to the kernel command line.

A secondary function of the bootloader is to provide a maintenance mode for updating boot configurations, loading new boot images into memory, and maybe running diagnostics.

### The Boot Sequence
In its simplest version, the bootloader is stored in a linearly addressable storage medium such as a non-volatile NOR flash memory. At boot time, a jump instruction at the reset vector (i. e., memory location where the instruction execution begins from) points to the start  of the bootloader in the NOR flash memory. One of the first tasks executed by the bootloader is to initialize the DRAM controller and copy itself into the DRAM. When executing from the DRAM, it loads the kernel from the flash memory to the DRAM and transfers control to the kernel.

#### Phased Boot Sequence
##### Phase 1: ROM Code
In the absence of reliable external memory, the first phase of bootup is handled by code that is loaded into the device's ROM. Such a ROM code does not typically include code to initialize the DRAM, but is limited to using the SRAM (which does not require a memory controller). The ROM code loads a small chunk of boot code from one of several pre-programmed locations into the SRAM. These locations include, NAND flash memory, SPI flash memory, or sections of an MMC device (eMMC or SD card). In SoCs where the SRAM is not large enough to load a full bootloader, an intermediate loader called the Secondary Program Loader (SPL) is loaded into the SRAM, and the execution jumps to the beginning of that code.

![[Pasted image 20230927140146.png]]
##### Phase 2: Secondary Program Loader
The SPL sets up the memory controller and other essential parts of the system in preparation for loading the Tertiary Program Loader (TPL) into the DRAM. Similar to the ROM code, it loads programs from a list of pre-programmed storage devices. In some cases, the SPL  may have filesystem drivers built into it, which enables it to read files from a disk partition.

##### Phase 3: Tertiary Program Loader
The TPL is a full bootloader which loads the kernel into DRAM. It could also provide a simple command-line user interface for performing maintenance tasks such as loading new boot and kernel images into flash, loading and booting a kernel, etc. At the end of the TPL, the kernel is in memory, waiting to be started.

![[Pasted image 20230927140615.png]]

Finally, the embedded bootloaders' memory is released and the control is transferred to the kernel.

#### Moving from Bootloader to Kernel
When transferring control to the kernel, the bootloader has to pass some basic information:
- *Machine number*: To identify the type of the SoC.
- Basic details of the hardware such as size and location of the physical RAM and CPU clock speed.
- Kernel command line: Which is a plain ASCII string that controls the behavior of Linux.
- (Optional) Location and size of device tree binary.
- (Optional) Location and size of an initial RAM disk, called the initial RAM file system (initramfs)
### [[Device Trees]]

### [[U-Boot]]