---
tags:
  - embeddedLinux
  - deviceDrivers
---
### Discovering the Hardware Configuration
Device drivers are usually written to interact with hardware. Part of that is being able to discover the hardware in the first place, bearing in mind that it may be at different addresses in different configurations.

In some cases, the hardware provides the information itself. Devices on a discoverable bus such as PCI or USB have a query mode, which returns resource requirements and a unique identifier. The kernel matches the identifier and possibly other characteristics with the device drivers and marries them up.

However, most of the hardware blocks on an embedded board do not have such identifiers. You have to provide the information yourself in the form of a [[Device Trees|device tree]] or as C structures known as [[Platform Data|platform data]].

In the standard driver model for Linux, device drivers register themselves with the appropriate subsystem: PCI, USB, open firmware (device tree), platform device, and so on. The registration includes an identifier and a callback function, called a `probe` function, that is called if there is a match between the ID of the hardware and the ID of the driver. For PCI and USB, the ID is based on the vendor and the product IDs of the devices; for device trees and platform devices, it is a name (a text string).
#### Linking Hardware with Device Drivers
The following is an example of an Ethernet driver code that works with both the device tree and platform data.
```c
static const struct of_device_id smc91x_match[] = {
	{ .compatible = "smsc,lan91c94", },
	{ .compatible = "smsc,lan91c111", },
	{},
};
MODULE_DEVICE_TABLE(of, smc91x_match);
static struct platform_driver smc_driver = {
	.probe = smc_drv_probe,
	.remove = smc_drv_remove,
	.driver = {
		.name = "smc91x",
		.of_match_table = of_match_ptr(smc91x_match),
	},
};

static int __init smc_driver_init(void) {
	return platform_driver_register(&smc_driver);
}

static void __exit smc_driver_exit(void)
{
	platform_driver_unregister(&smc_driver);
}
module_init(smc_driver_init);
module_exit(smc_driver_exit);
```
When the driver is initialized, it calls `platform_driver_register()`, pointing to struct `platform_driver`, in which there is a callback to a probe function, a driver name, `smc91x`, and a pointer to struct `of_device_id`.

If this driver has been configured by the device tree, the kernel will look for a match between the `compatible` property in the device tree node and the string being pointed to by the `compatible` structure element. For each match, it calls the `probe` function.

On the other hand, if it was configured through platform data, the `probe` function will be called for each match on the string pointed to by `driver.name`.