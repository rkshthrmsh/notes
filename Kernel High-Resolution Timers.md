---
tags:
  - kernel
---
#### Kernel High-Resolution Timers
While the [[Kernel Low-Precision Timers|legacy timer]] implementation is bound to ticks, high-precision timers provide us with nanosecond-level and tick-agnostic timing accuracy to meet the need for precise time applications or kernel drivers. The high-resolution timer interface comes with a new data type for keeping the time value: `ktime_t`, which is a simple 64-bit scalar.

`hrtimer` APIs require a `#include <linux/hrtimer.h>` header, and the structure characterizing high-resolution timers are:
```c
struct hrtimer {
	ktime_t _softexpires;
	enum hrtimer_restart (*function) (struct hrtimer *);
	struct hrtimer_clock_base *base;
	u8 state;
	[...]
};
```

Working with `hrtimer`:
- Before using, it must be initialized with `hrtimer_init()`.
- After the hrtimer has been initialized, it must be assigned a callback function that will be executed upon the timer expiration.
```c
enum hrtimer_restart callback(struct hrtimer *h);
```
- 
	- When restarting the timer with the callback, the timer restart is implicitly handled by the kernel. However, the driver needs to reset the timeout before returning from the callback. This can be done with `hrtimer_forward()` or `hrtimer_forward_now()`.
- An initialized `hrtimer` can be started with `hrtimer_start()`.
- In order to cancel a timer, drivers can use `hrtimer_cancel()` or `hrtimer_try_to_cancel()`.
- To check whether the `hrtimer` callback is still running, use `hrtimer_call_back_running()`.

An example:
```c
# include <linux/module.h>
# include <linux/kernel.h>
# include <linux/hrtimer.h>
# include <linux/ktime.h>

static struct hrtimer ht_timer;

static enum hrtimer_restart timer_callback(struct hrtimer *timer)
{
	pr_info("Hello from timer!\n");
# ifdef PERIODIC_MS_500
	hrtimer_forward_now(timer, ms_to_ktime(500));
	return HRTIMER_RESTART;
# else
	return HRTIMER_NORESTART
# endif
}

static int __init hrtimer_module_init(void)
{
	ktime_t init_time;
	init_time = ktime_set(1, 1000);
	hrtimer_init(&hr_timer, CLOCK_MONOTONIC, HRTIMER_MODE_REL);
	hr_timer.function = &timer_callback;
	hrtimer_start(&hr_timer, init_time, HRTIMER_MODE_REL);
	return 0;
}

static void __exit hrtimer_module_exit(void)
{
	int ret;
	ret = hrtimer_cancel(&hr_timer);
	if (ret)
		pr_info("our timer is still in use...\n");
	pr_info("Uninstalling hrtimer module\n");
}

module_init(hrtimer_module_init);
module_exit(hrtimer_module_exit);
# MODULE INFO
[...]
```