---
tags:
  - kernel
---
#### Mutex
A mutex is another locking primitive that behaves similar to the [[Spinlock|spinlock]], with the only difference being that the code can sleep. Therefore, when a task tries to lock a mutex that is already held by another task, the task will get suspended and woken up only when the mutex is released.

A mutex is a simple data structure that embeds a wait queue (to put contenders to sleep) and a spinlock to protect access to this wait queue.
```c
struct mutex {
	atomic_long_t owner;
	spinlock_t wait_lock;
#ifdef CONFIG_MUTEX_SPIN_ON_OWNER
	struct optimistic_spin_queue osq;
#eindif
	struct list_head wait_list;
}
```

A mutex is created statically by the `DEFINE_MUTEX()` macro or dynamically by the `mutex_init()` function.
```c
# Static mutex
static DEFINE_MUTEX(my_mutex);

# Dynamic allocation
struct fake_data {
	struct i2c_client *client;
	u16 reg_conf;
	struct mutex mutex;
};
static int fake_probe(struct i2c_client *client)
{
[...]
	mutex_init(&data->mutex);
[...]
}
```

Acquiring a lock on a mutex is as simple as calling one of:
```c
void mutex_lock(struct mutex *lock);
int mutex_lock_iterruptible(struct mutex * lock);
int mutex_lock_killable(struct mutex *lock);
int mutex_trylock(struct mutex *lock);
```
If the mutex is free (unlocked), your task will immediately acquire it without going to sleep. Otherwise, your task will be put to sleep in a manner that depends on the locking function you use. With `mutex_lock()`, your task will be put in an uninterruptible sleep state (`TASK_UNINTERRUPTIBLE`) while waiting for the mutex to be released (if it is held by another task). `mutex_lock_interruptible()` will put your task in an interruptible sleep state, in which the sleep can be interrupted by any signal. `mutex_ lock_killable()` will allow your sleeping task to be interrupted only by signals that actually kill the task. Each of these functions returns 0 if the lock has been acquired successfully. Moreover, interruptible variants return `-EINTR` when the locking attempt was interrupted by a signal.

Whichever locking function is used, the mutex owner (and only the owner) should release the mutex using `mutex_unlock()`, defined as follows:
```c
void mutex_unlock(struct mutex *lock);
```

Checking the status of a mutex can be done by:
```c
static bool mutex_is_locked(struct mutex *lock);
```