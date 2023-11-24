---
tags:
  - kernel
---
### Linux Kernel Time Management
Time is one of the most used resources in computer systems, right after memory. It is used to do almost everything: timer, sleep, scheduling, and many other tasks.

In the original Linux timer implementation, the main hardware timer was used for timekeeping. It was programmed to fire interrupts periodically at HZ frequency, whose corresponding period is called a jiffy. Each of these interrupts generated every 1/HZ second was (and still is) referred to as a tick. The whole system time management (either from the kernel or user space) was bound to jiffies, which is also a global variable in the kernel, incremented at each tick. In addition to incrementing the value of `jiffies`, the tick handler was also responsible for processes scheduling, statistic updating, and profiling.

Starting from kernel version 2.6.21, hrtimers were introduced. This resulted in better granularity and accuracy than with the jiffy resolution.
#### [[Clocksource Framework and Clock Source Devices]]
#### [[Clockevent Framework and Clock Event Devices]]
#### Tick Devices
Tick devices are software extensions of [[Clockevent Framework and Clock Event Devices|clock event devices]] to provide a continuous stream of tick events that happen at regular time intervals. A tick device is automatically created by the kernel when a new clock event device is registered and always selects the best clock event device.

The definition of a tick-device data structure is shown in the following code snippet:
```c
struct tick_device {
	struct clock_event_device *evtdev;
	enum tick_device_mode mode;
}
```
In this data structure, evtdev is the clock event device that is abstracted by the tick device. mode is used to track the working mode of the underlying clock event.
![[Pasted image 20231006134215.png]]

A tick device can either be global to the system or local, i.e., per-CPU tick device:
- A per-CPU tick device is used to provide local CPU functionality such as process accounting, profiling, and CPU local periodic tick (in periodic mode) and CPU local next event interrupt (in non-periodic mode)
- A global tick device is responsible for providing the period ticks that mainly run the `do_timer()` and `update_wall_time()` functions. Thus, the first one updates the global `jiffies` value and updates the system load average, while the latter updates the wall time and runs any dynamic timers that have expired.
#### Broadcast Tick Devices
On platforms implementing CPU power management, most of the hardware timers backing clock event devices will be turned off in some `CPUidle` states. To keep the software timers functional, the kernel relies on an always-on clock event device (that is, backed by an always-on timer) to be present in the platform to relay the interrupt signaling when the timer expires. This always-on timer is called a **broadcast clock device**. In a nutshell, a tick broadcast is used to wake idle CPUs. It is represented by the broadcast directory in `/sys/devices/system/clockevents/`. It must be noted that the [[Clocksource Framework and Clock Source Devices|clock source]], [[Clockevent Framework and Clock Event Devices|clock event]], and broadcast device timers are all different.
#### `sched_clock()`
`sched_clock()` is a kernel timekeeping and time-stamping function that returns the number of nanoseconds since the system started. The timer driving `sched_clock()` has to be very fast compared with the clock source timers. As its name states, it is mainly used by the scheduler, which means it is called much more often.
#### Dynamic Tick / Tickless Kernel
Dynamic ticks are the logical consequence of migration to a high-resolution timer interface. Before they were introduced, periodic ticks periodically issued interrupts (HZ times per second) to drive the operating system. This kept the system awake even when there were no tasks or timer handlers to run. With this approach, long rests were impossible for the CPUs, which had to be awake for no purpose.

They dynamic tick mechanism allows periodic ticks to be stopped during certain time intervals to save power. With this new approach, periodic ticks are enabled back only when some tasks need to be performed.

When a CPU enters an idle state, the timer framework examines the next scheduled timer event and, if it is later than the next periodic tick, it reprograms the per-CPU clock event device to this later event. This will allow the idle CPU to enter longer idle sleeps without being interrupted unnecessarily by a periodic tick. There are, however, systems with low power states where even the per-CPU clock event device would stop. On such platforms, it is the [[#Broadcast Tick Devices|broadcast tick device]] that is programmed with the next future event.
#### [[Kernel Low-Precision Timers]]
#### [[Kernel High-Resolution Timers]]