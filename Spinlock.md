---
tags:
  - embeddedLinux
  - kernel
---
#### Spinlock
A spinlock is a hardware-based locking primitive that depends on hardware capabilities to provide atomic operations. It is the simplest locking primitive. It is a lock held by a CPU, in contrast to a [[Mutex|mutex]], which is a lock held by a thread.

A spinlock operates by disabling the scheduler on the local CPU (that is, the CPU running the task that called the spinlock's locking API). This also means that a task currently running on that CPU cannot be preempted except by interrupt requests (IRQs) if they are not disabled on the local CPU.

A spinlock is created either statically using a `DEFINE_SPINLOCK` macro or dynamically by calling spin_lock_init() on an uninitialized spinlock.
```c
# Static spinlock
static DEFINE_SPINLOCK(my_spinlock)

# For dynamic allocation, it is better to embed the spinlock in a bigger structure, allocating memory for this structure, and then initializing the spinlock.
struct bigger_struct {
	spinlock_t lock;
	unsigned int foo;
	[...]
}
static struct bigger_struct *fake_init_function()
{
	struct bigger_struct *bar;
	bar = kmalloc(sizeof(struct_bigger_struct), GFP_KERNEL):
	if (!bar)
		return -ENOMEM
	spin_loc_init(&bar->lock);
	return bar;
}
```

There are many different variants of lock and unlock functions for spinlocks:
- `spin_lock()` and `spin_unlock()`: These are the simplest variants. However, they have some limitations. Though a spinlock prevents preemption on the local CPU, it does not prevent this CPU from being hogged by an interrupt (thus executing this interrupt's handler). Imagine a situation where the CPU holds a spinlock on behalf of a task in order to protect a given resource, and an interrupt occurs. The CPU will stop its current task and branch to this interrupt handler. So far, so good. Now, imagine if this IRQ handler needs to acquire this same spinlock. It will infinitely spin in place, trying to acquire a lock already locked by a task that it has preempted. This situation will result in a deadlock.
- `spin_lock_irq()` and `spin_unlock_irq()`: To mitigate the issue of IRQ handlers locking themselves out of spinlocks, the kernel provides IRQ safe variants. However, these only partially solve the problem. Imagine interrupts are already disabled on the processor before your code starts locking; when you call `spin_unlock_irq()`, you will not just release the lock but enable interrupts also, but probably in an erroneous manner since there is no way for `spin_unlock_irq()` to know which interrupts were enabled before locking and which were not.
- `spin_lock_irqsave()` and `spin_unlock_irqsave()`: These variants save the state of the interrupts in a variable before acquiring the lock and restore them exactly as they were while releasing the lock.
- `spin_trylock()`: There are cases where we may need to acquire the lock only if it is not already held by another contender elsewhere. Such methods try to acquire the lock and immediately return a status value, showing whether the lock as been successfully acquired or not.