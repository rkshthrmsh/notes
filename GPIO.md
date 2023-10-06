---
tags:
  - embeddedLinux
  - deviceDrivers
---
#### GPIO
General-Purpose Input/Output (GPIO) is the simplest form of digital interface since it gives direct access to individual hardware pins, each of which can be in one of two states: either high or low. In most cases, a GPIO pin can be configured to be either an input or an output. A group of GPIO pins can be used to create higher level interfaces such as I2C or SPI by manipulating each bit in software, a technique that is called **bit banging**. The main limitation is the speed and accuracy of the software loops and the number of CPU cycles needed to dedicate to them. Generally speaking, it is hard to achieve timer accuracy better than a millisecond unless you configure a real-time kernel

Most SoCs have a lot of GPIO bits, which are grouped together in GPIO registers, usually 32 bits per register. On-chip GPIO bits are routed through to GPIO pins on the chip package via a multiplexer, known as a **pin mux**. There may be additional GPIO pins available off-chip in the power management chip, and in dedicated GPIO extenders, connected through I2C or SPI buses. All this diversity is handled by a kernel subsystem known as `gpiolib`, which is not actually a library but the infrastructure GPIO drivers used to expose I/O in a consistent way. Applications can interact with the `gpiolib` through files in the [[`/sys/class/gpio`]] directory.
```bash
ls /sys/class/gpio
```
Looking within one of the gpio directories shows attributes (files). The file named `base` contains the number of the first GPIO pin in the register, while `ngpio` contains the number of bits in the register.

To control a GPIO bit from user space, it must first be exported from kernel space, which can be done by writing the GPIO number to `/sys/class/gpio/export`. For example, for GPIO 53:
```bash
echo 53 > /sys/class/gpio/export
```
This creates a new directory called `gpio53` which contains:
```
ls /sys/class/gpio/gpio53
active_low direction power uevent
device edge subsystem value
```
The pin begins as an input. To change it to an output, write `out` to the `direction` file. The file `value` contains the current state of the pin, which is `0` for low and `1` for high. If it is an output, you can change the state by writing `0` or `1` to `value`. The GPIO can be removed from user space control by writing the GPIO number to `/sys/class/gpio/unexport`.

If the GPIO bit can generate interrupts, a file called `edge` exists. Initially, it has the value called `none`, meaning that it does not generate interrupts. To enable interrupts, `edge` can be set to:
- `rising`: Interrupt on rising edge
- `falling`: Interrupt on falling edge
- `both`: Interrupt on both rising and falling edge
- `none` (default): No interrupts
Handling interrupts can be done by using `epoll` of a combination of `select` and `poll`. The performance of `epoll` does not degrade rapidly as compared to the other two system calls as the number of file descriptors being monitored increases. An example using `epoll` is:
```c
/* Copyright (C) 2020, Frank Vasquez (frank.vasquez@gmail.com) */

#include <stdio.h>
#include <fcntl.h>
#include <unistd.h>
#include <sys/epoll.h>
#include <sys/types.h>

/*
 * Demonstration of using epoll(2) to wait for an interrupt on GPIO.
 *
 * To try this out on a BeagleBone Black, connect a push button switch
 * between P9 15 (gpio1_16) and P9 1 (ground).
 *
 * gpio1_15 is configured as gpio 48, so to make it an input which
 * triggers on a falling edge, write
 *
 * echo 48 > /sys/class/gpio/export
 * echo falling > /sys/class/gpio/gpio48/edge
 *
 * Now, the gpio1_15 pin is normally pulled high, so
 * /sys/class/gpio48/value reads as 1'.
 * Pushing the button takes it low, and value reads as '0'.
 *
 * This program waits for the level to fall from 1 to 0 and
 * prints out a message each time it does so.
 */

int main(int argc, char *argv[])
{
	int ep;
	int f;
	struct epoll_event ev, events;
	char value[4];
	int ret;
	int n;

	ep = epoll_create(1);
	if (ep == -1) {
		perror("Can't create epoll");
		return 1;
	}

	f = open("/sys/class/gpio/gpio48/value", O_RDONLY | O_NONBLOCK);
	if (f == -1) {
		perror("Can't open gpio48");
		return 1;
	}

	n = read(f, &value, sizeof(value));
	if (n > 0) {
		printf("Initial value value=%c\n",
		       value[0]);
		lseek(f, 0, SEEK_SET);
	}

	ev.events = EPOLLPRI;
	ev.data.fd = f;
	ret = epoll_ctl(ep, EPOLL_CTL_ADD, f, &ev);
	if (ret == -1) {
		perror("Can't register target file descriptor with epoll");
		return 1;
	}

	while (1) {
		printf("Waiting\n");
		ret = epoll_wait(ep, &events, 1, -1);

		if (ret > 0) {
			n = read(f, &value, sizeof(value));
			printf("Button pressed: read %d bytes, value=%c\n",
			       n, value[0]);
			lseek(f, 0, SEEK_SET);
		}
	}

	return 0;
}
```