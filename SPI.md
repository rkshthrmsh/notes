---
tags:
  - embeddedLinux
  - deviceDrivers
---
#### SPI
The interface uses four wires with separate send and receive lines, which allow it to operate in full duplex. Each chip on the bus is selected with a dedicated chip select line. It is commonly used to connect to touchscreen sensors, display controllers, and serial NOR flash devices.

As with [[I2C]], it is a master-slave protocol, with most SoCs implementing one or more master host controllers. There is a generic SPI device driver, which you can enable through the `CONFIG_SPI_SPIDEV` kernel configuration. It creates a device node for each SPI controller, which allows you to access SPI chips from the user space. The device nodes are named `spidev[bus].[chip select]`.
```
$ ls /dev/spi*
/dev/spidev1.0
```