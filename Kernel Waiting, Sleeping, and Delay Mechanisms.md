---
tags:
  - kernel
---
### Kernel Waiting, Sleeping, and Delay Mechanisms
The term *sleeping* refers to a mechanism by which a task (on behalf of the running kernel code) voluntarily relaxes the processor, with the possibility of another task being scheduled.
#### [[Wait Queue]]
#### Simple Sleeping in the Kernel
This simple sleeping can also be referred to as passive delay because the task sleeps (allowing the CPU to schedule another task) while waiting, in contrast to active delay, which is a busy wait as the task waits by wasting the CPU clock and thus consuming resources.

Before using sleeping APIs, the driver must include `#include <linux/ delay>`, which would make the following function available:
```c
usleep_range(unsigned long min, unsigned long max)
msleep(unsigned long msecs)
msleep_interruptible(unsigned long msecs)
```
The `usleep_range()` API relies on high-resolution timers (hrtimers), and it is recommended to use this sleep for a few ~μsecs or small msecs (between 10 microseconds and 20 milliseconds), avoiding the busy-wait loop of `udelay()`. `msleep*()` APIs are backed by jiffies/legacy timers. You should use this for larger milliseconds of sleep (10 milliseconds or more).

**NOTE**: APIs in this section must be used for inserting delays in a non-atomic context exclusively.
#### Busy Waiting in the Kernel
Busy waiting is when the task actively waits, consuming CPU resources. The drivers for busy waiting are also included in `<linux/delay>`:
```c
ndelay(unsigned long nsecs)
udelay(unsigned long usecs)
mdelay(unsigned long msecs)
```
- `ndelay`: The precision of `ndelay` depends on how accurate your timer is (not always the case on an embedded system on a chip, or SoC). `ndelay`-level precision may not actually exist on many non-PC devices.
- `udelay`: It will busy wait for enough loop cycles to achieve the desired delay. You should use this function if you need to sleep for a few μsecs (< ~10 microseconds). It is recommended to use this API even for sleeping less than 10 us because, on slower systems (some embedded SoCs), the overhead of setting up hrtimers for `usleep` may not be worth it.
- `mdelay`: This is a macro wrapper around `udelay` to account for possible overflow when passing large arguments to `udelay`. In general, the use of `mdelay` is discouraged, and code should be refactored to allow for the use of `msleep`.

These APIs can be used in both atomic and non-atomic contexts.