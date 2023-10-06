---
tags:
  - embeddedLinux
  - deviceDrivers
---
#### I2C
I2C is a simple low speed 2-wire bus that is common on embedded boards, typically used to access peripherals that are not on the SoC, such as display controllers, camera sensors, GPIO extenders, and so on. There is a related standard known as **system management bus** (**SMBus**) that is found on PCs, which is used to access temperature and voltage sensors. SMBus is a subset of I2C.

I2C is a master-slave protocol, with the master being one or more host controllers on the SoC. Slaves have a 7-bit address assigned by the manufacturer, allowing up to 128 nodes per bus, but 16 are reserved, so only 112 nodes are allowed in practice. The master may initiate read or write transactions with one of the slaves. Frequently, the first byte is used to specify a register on the slave, while the remaining bytes are the data that's read from or written to that register.

There is one device node for each host controller. The device interface provides a series of `ioctl` commands that query the host controller and send the read and write commands to I2C slaves. A package called `i2c-tools` uses this interface to provide basic command-line tools to interact with I2C devices:
- `i2cdetect`: List I2C adapters and probes on the bus.
- `i2cdump`: Dump data from all registers of an I2C peripheral.
- `i2cget`: Reads data from an I2C slave.
- `i2cset`: Write data to an I2C slave.

The following is an example of writing to and reading from an I2C slave nativel (without `i2c-tools`):
```c
/* Copyright (C) 2017, Chris Simmonds (chris@2net.co.uk) */

/*
 * Sample program to read the first 4 bytes of the AT24C512B
 * 512 KiB EEPROM from the BeagleBone Black
 * The datasheet for the AT24C512B can be found at
 * http://www.atmel.com/Images/doc5297.pdf
 */

#include <stdio.h>
#include <unistd.h>
#include <fcntl.h>
#include <sys/ioctl.h>
#include <linux/i2c-dev.h>

/* Address of the EEPROM on the BeagleBone Black board */
#define I2C_ADDRESS 0x50

int main(void)
{
	int f;
	int n;
	char buf[10];

	/* Open the adapter and set the address of the I2C device */
	f = open("/dev/i2c-0", O_RDWR);
	if (f < 0) {
		perror("/dev/i2c-0:");
		return 1;
	}

	/* Set the address of the i2c slave device */
	if (ioctl(f, I2C_SLAVE, I2C_ADDRESS) == -1) {
		perror("ioctl I2C_SLAVE");
		return 1;
	}

	/* Set the 16-bit address to read (0) */
	buf[0] = 0;	/* address byte 1 */
	buf[1] = 0;	/* address byte 2 */
	n = write(f, buf, 2);
	if (n == -1) {
		perror("write");
		return 1;
	}

	/* Now read 4 bytes from that address */
	n = read(f, buf, 4);
	if (n == -1) {
		perror("read");
		return 1;
	}
	printf("0x%x 0x%x0 0x%x 0x%x\n",
		 buf[0], buf[1], buf[2], buf[3]);

	close(f);
	return 0;
}
```