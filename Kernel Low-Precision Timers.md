---
tags:
  - kernel
---
#### Kernel Low-Precision Timers
Standard (now legacy and also referred to as low-resolution) timers are kernel timers operating on the granularity of `jiffies`. A jiffy is a kernel unit of time whose duration depends on the value of `HZ`, which represents the incrementation frequency of the `jiffies` variable in the kernel. Each increment event is called a tick.

A timer is represented in the kernel as an instance of `struct timer_list`:
```c
struct timer_list {
	struct hlist_node entry;
	unsigned long expires;
	void (*function) (struct timer_list *);
	u32 flags
}
```
Here, `expires` is an absolute value in jiffies that defines when this timer will expire in the future. `entry` is internally used by the kernel to track this timer in a per-CPU global list of timers. `flags` are OR'ed bitmasks that represent the timer flags, such as the way the timer is managed and the CPU on which the callback will be scheduled, and `function` is the callback to be executed when this timer expires.

Working with timers:
- Timers can be created dynamically or statically using the `DEFINE_TIMER` macro or `timer_setup()` functions, respectively.
- After the timer has been initialized, its expiration delay must be set before starting it. This can be done with `mod_timer()` or `add_timer()`.
- Once done with the timer, it can be released or cancelled using `del_timer()` or `del_timer_sync()`. When `del_timer()` returns, it only guarantees that the timer is deactivated and unqueued, ensuring that it will not be executed in the future. However, on a multiprocessing machine, the timer function might already be executing on another processor. `del_timer_sync()` should be used in such cases, which will deactivate the timer and wait for any executing handler to exit before returning.
- To check whether the timer is pending, use `timer_pending()`.

An example of a basic usage of timers:
```c
#include <linux/init.h>
#include <linux/kernel.h>
#include <linux/module.h>
#include <linux/timer.h>

static struct timer_list my_timer;

void my_timer_callback(struct timer_list *t)
{
	pr_info("Timer callback&; called\n");
}

static int __init my_init(void)
{
	int retval;
	pr_info("Timer module loaded\n");
	timer_setup(&my_timer, my_timer_callback, 0);
	pr_info("Setup timer to fire in 500ms (%ld)\n", jiffies);
	retval = mod_timer(&my_timer, jiffies + msecs_to_jiffies(500));
	if (retval)
		pr_info("Timer firing failed\n");
	return 0;
}

static void my_exit(void)
{
	int retval;
	retval = del_timer(&my_timer);
	if (retval)
		pr_info("The timer is still in use...\n");
	pr_info("Timer module unloaded\n");
}

module_init(my_init);
module_exit(my_exit);
MODULE_AUTHOR("John Madieu <john.madieu@gmail.com>");
MODULE_DESCRIPTION("Standard timer example");
MODULE_LICENSE("GPL");
```