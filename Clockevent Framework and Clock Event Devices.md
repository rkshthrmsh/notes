---
tags:
  - kernel
---
#### `Clockevent` Framework and Clock Event Devices
Before the concept of clockevent was introduced, the locality of hardware timers was not considered. The [[Clocksource Framework and Clock Source Devices|clock source]] hardware was programmed to periodically generate HZ ticks (interrupts) per second, the interval between each tick being a jiffy. With the introduction of clockevent in the kernel, the interruption of the clock became abstracted as an event. The main function of the clockevent framework is to distribute the clock interrupts (events) and set the next trigger condition. It is a generic framework for next-event interrupt programming.

A clock event device is a device that can fire interrupts and allow us to program when the next interrupt (an event) will poke in the future. Clock event devices are orthogonal to clock source devices.
```
# List available clock event devices
$ ls /sys/devices/system/clockevents/
broadcast   clockevent1 power
clockevent0 clockevent2 uevent

# Check underlying device for clockevent
$ ls /sys/devices/system/clockevents/clockevent0/current_device
arch_sys_timer
```

Some points worth noting are:
- There are as many clock event devices as CPUs on the system (allowing per-CPU clock devices, thus involving timer locality).
- Thee is always a `broadcast` directory.
- All clock event devices are backed by the same hardware timer, which is different from the hardware timer backing the [[Clocksource Framework and Clock Source Devices|clock source]] device.
- At least two hardware timers are needed to support the high-resolution timer interface, one playing the clock source role and one (ideally per-CPU) baking clock event devices.

Clock event devices can be configured to work in either periodic or one-shot mode:
- In periodic mode, it is configured to generate a tick every 1/HZ second and does all the things the [[Kernel Low-Precision Timers|legacy (low-resolution) timer]]-based tick did, such as updating jiffies, accounting CPU time, and so on. In other words, in periodic mode, it is used the same way as the legacy low-resolution timer was, but it is run out of the new infrastructure.
- One-shot mode makes the hardware generate a tick after a specific number of cycles from the current time. It is mostly used to program the next interrupt that will wake the CPU before it goes idle.