---
tags:
  - kernel
---
#### Wait Queue
The kernel scheduler manages a list of tasks to run (tasks in a `TASK_RUNNING` state), known as a runqueue. On the other hand, sleeping tasks, whether interruptible or not (in a `TASK_INTERRUPTIBLE` or `TASK_UNINTERRUPTIBLE` state), have their own queues, known as wait queues.

Wait queues are a higher-level mechanism essentially used to process blocking input/ output (I/O), to wait for a condition to be true, to wait for a given event to occur, or to sense data or resource availability.

A wait queue head is defined as follows:
```c
struct wait_queue_head {
	spinlock_t lock;
	struct list_head head;
}
```
A wait queue is nothing but a list (with sleeping processes in it waiting to be awakened) and a [[Spinlock|spinlock]] to protect access to this list. We can use a wait queue when more than one process wants to sleep, waiting for one or more events to occur in order to be awakened. The head member is actually a list of processes waiting for the event(s). Each process that wants to sleep while waiting for the event to occur puts itself it this list before going to sleep. When an event occurs, one or more processes on the list are woken up and moved off the list.

A wait queue can be initialized in two ways:
```c
# Statically
DECLARE_WAIT_QUEUE_HEAD(my_event);

# Dynamically
wait_queue_head_t my_event;
init_waitqueue_head(&my_event);
```

Any process that wants to sleep while waiting for my_event to occur can invoke either `wait_event_interruptible()` or `wait_event()`. These functions both take an expression in place of the second argument so that the process is put to sleep only if the expression evaluates false. A process that is put to sleep is removed from the runqueue.

There may be cases where you need not only the condition to be true but to time out after a certain waiting duration. You can address such cases using `wait_event_ timeout()`
```
wait_event_timeout(wq_head, condition, timeout)
```

After a change on any variable that could affect the result of the wait condition, you must call the appropriate `wake_up*` family function. That being said, in order to wake up a process sleeping on a wait queue, you should call either `wake_up()`, `wake_up_all()`, `wake_up_interruptible()`, or `wake_up_interruptible_all()`. Whenever you call any of these functions, the condition is re-evaluated again. If the condition is true at that time, then a process (or all processes for the `_all()` variant) in the wait queue will be awakened, and its (their) state set to `TASK_RUNNING`; otherwise, (the condition is false), nothing happens.
```c
wake_up(&my_event);
wake_up_all(&my_event);
wake_up_interruptible(&my_event);
wake_up_all_interruptible(&my_event);
```
Since the `_interruptible` versions can be interrupted by signals, their return values should be checked. A non-zero value means the sleep has been interrupted by a signal, and the driver will return `ERESTARTSYS`.