---
tags:
  - embeddedLinux
  - deviceDrivers
---
#### LEDs
LEDs are often controlled through GPIO pins, however, the `leds` kernel subsystem offers more specialized ways of controlling them. Using `leds` provides the ability to control the brightness, event-based triggers, etc.
As with [[GPIO]]s, LEDs are controlled through an interface in `sysfs` in the `/sys/class/leds` directory. The names of the LEDs are encoded in the device tree in the form of `devicename:colour:function`. For example, on the BeagleBone:
```
$ ls /sys/class/leds
beaglebone:green:heartbeat beaglebone:green:usr2
beaglebone:green:mmc0 beaglebone:green:usr3

$ cd /sys/class/leds/beaglebone\:green\:usr2
$ ls
brightness max_brightness subsystem uevent
device power trigger
```
The `brightness` file controls the brightness of the LED and can be a number between `0` (off) and `max_brightness` (fully on). If the LED doesn't support intermediate brightness, any non-zero value turns it on. The file called `trigger` lists the events that trigger the LED to turn on. The list of triggers is implementation-dependent. An example is:
```
$ cat trigger
none mmc0 mmc1 timer oneshot heartbeat backlight gpio [cpu0]
default-on
```
The trigger currently selected is shown in square brackets. You can change it by writing one of the other triggers to the file. If you want to control the LED entirely through brightness, select none. If you set trigger to timer, two extra files will appear that allow you to set the on and off times in milliseconds:
```
$ echo timer >trigger
$ ls
brightness delay_on max_brightness subsystem uevent
delay_off device power trigger
```