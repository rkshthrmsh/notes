---
tags:
  - kernel
---
#### Softirqs
As the name suggests, softirq stands for software interrupt. Softirq handlers can preempt all other tasks on the system but the hardware IRQ handlers since it is executed with IRQs enabled. Softirqs are intended to be used for high-frequency threaded job scheduling. The softirq APIs are implemented in `kernel/softirq.c` in the kernel source tree, and drivers that wish to use this API need to include `<linux/interrupt.h>`.

Softirqs are represented by the `softirq_action` structure which is defined as:
```c
struct softirq_action {
	void (*action) (struct softirq_action *);
};
```
This structure embeds a pointer to the function to run when the softirq is raised. Thus, a prototype of a softirq handler would look like:
```c
void softirq_handler(struct softirq_action *h);
```
Running a softirq handler results in executing this action function, which has only one parameter: a pointer to the corresponding `softirq_action` structure. You can register the softirq handler at runtime by means of the `open_softirq()` function.
```c
void open_softirq(int nr, void (*action) (struct softirq_action *))
```
`nr` represents the softirq index, which is also considered as the softirq priority (where 0 is the highest). Softirqs with lower indexes (highest priority) run before those with higher indexes (lowest priority). `action` is a pointer to the softirq handler.

Before a registered softirq can execute, it should be activated/scheduled. In order to do so, you have to call `raise_softirq()` or `raise_softirq_irqoff()`.

Some characteristics of softirqs:
- A softirq can never preempt another softirq. Only hardware interrupts can. Softirqs are executed at a high priority, with scheduler preemption disabled but IRQs enabled. This makes softirqs suitable for the most time-critical and important deferred processing on the system.
- While a handler runs on a CPU, other softirqs on this CPU are disabled. Softirqs run concurrently, however. While a softirq is running, another softirq (even the same one) can run on another processor. This is one of the main advantages of softirqs over hardirqs and is the reason why they are used in the networking subsystem, which may require heavy CPU power.
- Softirqs are mostly scheduled in the return path of hardware interrupt handlers. If any is scheduled out of the interrupt context, it will run in a process context if it is still pending when the local ksoftirqd thread (***ksoftirqd*** is a per-CPU kernel thread raised to handle unserviced software interrupts.) is given to the CPU.