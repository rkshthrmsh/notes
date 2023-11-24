---
tags:
  - kernel
---
#### `Clocksource` Framework and Clock Source Devices
A clock source is a monotonic, atomic, and free-running counter. In the kernel, there is a `clocksource_list` global list that tracks the clock source devices registered with the system, ordered by rating. The registering is done with either `clocksource_mmio_init()` or `clocksource_register_hz()` functions.

The preferred way to enumerate the available clock sources on a Linux system is by reading the contents of the `available_clocksource` file in `/sys/devices/system/clocksource/clocksource0/`. Similarly the current clock source can be viewed with the `current_clocksource` file. For example:
```
$ cat /sys/devices/system/clocksource/clocksource0/available_clocksource
arch_sys_counter

$ cat /sys/devices/system/clocksource/clocksource0/current_clocksource
arch_sys_counter

# Change clock source
echo <new_clocksource> > /sys/devices/system/clocksource/clocksource0/current_clocksource
```

One of the main goals of the clock source device is feeding the timekeeper. There can be multiple clock sources in a system, but the timekeeper will choose the one with the highest precision to use. The timekeeper needs to obtain the value of the clock source periodically to update the system time, which is usually updated during the tick processing.
![[Pasted image 20231006132901.png]]

The timekeeper provides several types of time:
- **xtime**: This is wall (real) time, which represents the current time as given by the Real-Time Clock (RTC) chip.
- **Monotonic time**: The cumulative time since the system is turned on, but does not count the time the system sleeps.
- **Raw monotonic time**: This has the same meaning as monotonic time, but it is purer and will not be affected by Network Time Protocol (NTP) time adjustment.
- **Boot time**: This adds the time the system spent sleeping to the monotonic time, which gives the total time after the system is powered on.

The following table shows the different types of time and their kernel getter functions:
![[Pasted image 20231006133135.png]]