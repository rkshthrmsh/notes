---
tags:
  - embeddedLinux
  - deviceDrivers
---
### Platform Data
In the absence of [[Device Trees|device tree]] support, there is a fallback method of describing hardware using C structures, known as the platform data. Each piece of hardware is described by `struct platform_device`, which has a name and a pointer to an array of resources. The resource's type is determined by flags, which include the following:
- `IORESOURCE_MEM`: This is the physical address of a region of memory.
- `IORESOURCE_IO`: This is the physical address or port number of I/O registers.
- `IORESOURCE_IRQ`: This is the interrupt number.

An example of platform data for an Ethernet controller is:
```c
#define VERSATILE_ETH_BASE0x10010000
#define IRQ_ETH 25
static struct resource smc91x_resources[] = {
	[0] = {
		.start = VERSATILE_ETH_BASE,
		.end = VERSATILE_ETH_BASE + SZ_64K - 1,
		.flags = IORESOURCE_MEM,
	},
	[1] = {
		.start = IRQ_ETH,
		.end = IRQ_ETH,
		.flags = IORESOURCE_IRQ,
	},
};
static struct platform_device smc91x_device = {
	.name = "smc91x",
	.id = 0,
	.num_resources = ARRAY_SIZE(smc91x_resources),
	.resource = smc91x_resources,
};
```

It has a memory area of 64 KB and an interrupt. The platform data has to be registered with the kernel, usually when the board is initialized
```c
void __init versatile_init(void) { platform_device_register(&versatile_flash_device); platform_device_register(&versatile_i2c_device); platform_device_register(&smc91x_device); [...]
```