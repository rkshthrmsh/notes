---
tags:
  - kernel
---
#### Workqueues
Workqueue is a simple work deferring mechanism that runs only in a preemptible context. At the core of the workqueue subsystem, there are two data structures:
- The work to be deferred (referred to as a work item), represented in the kernel by instances of struct `work_struct`, which indicates the handler function to run. If you need a delay before the work runs after it has been submitted to the workqueue, the kernel provides a struct `delayed_work` instance instead. A work item is a simple structure that only contains a pointer to the function that is to be scheduled for asynchronous execution. To summarize, we can enumerate two types of work item structures, as follows:
	- A `work_struct` structure, which schedules a task to run as soon as possible when the system allows it.
	- A `delayed_work` structure, which schedules a task to run after at least a given time interval.
- The workqueue itself, which is represented by a struct `workqueue_struct` instance and is the structure onto which work is placed. It is a queue of work items.

Using workqueues:
- The first step in using workqueues consists of creating a work item, represented by struct `work_struct` or struct `delayed_work` for the delayed variant, and defined in `linux/workqueue.h`.
```c
# Declare statically
DECLARE_WORK(name, function)
DECLARE_DELAYED_WORK(name, function)

# Declare dynamically
INIT_WORK(work, func);
INIT_DELAYED_WORK(work, func);
```

- Workqueue infrastructure allows drivers to create a dedicated kernel thread (a workqueue) called a worker thread to run work functions. A new workqueue can be created with:
```c
struct workqueue_struct *create_workqueue(const char *name)
struct workqueue_struct *create_singlethread_workqueue(const char *name)
```
`create_workqueue()` creates a dedicated thread (a worker thread) per CPU on the system. In most cases, a single system kernel thread should be enough. In this case, you should use `create_singlethread_ workqueue()` instead. 

- In order to schedule works on your created workqueue, you can use either `queue_work()` or `queue_delayed_work()`, depending on the nature of the work.
```c
bool queue_work(struct workqueue_struct *wq, struct work_struct *work)
bool queue_delayed_work(struct workqueue_struct *wq,
						struct delayed_work *dwork,
						unsigned long delay)
```
These functions return false if the work was already on a queue and true otherwise. The time unit for the delay is a jiffy.

- Submitted work items can be canceled by calling either `cancel_delayed_work()`, `cancel_ delayed_work_sync()`, or `cancel_work_sync()`.
```c
bool cancel_work_sync(struct work_struct *work)
bool cancel_delayed_work(struct delayed_work *dwork)
bool cancel_delayed_work_sync(struct delayed_work *dwork)
```
`cancel_work_sync()` synchronously cancels the given work—in other words, it cancels work and waits for its execution to finish. The kernel guarantees work not to be pending or executing on any CPU on return from this function, even if the work migrates to another workqueue or requeues itself. It returns true if work was pending and false otherwise. `cancel_delayed_work()` asynchronously cancels a delayed entry. It returns true (actually, a nonzero value) if dwork was pending and canceled and false if it wasn't pending, probably because it is actually running, and thus might still be running after `cancel_delayed_work()` has returned.

- To make sure work really ran to its end, use `flush_workqueue()`, which flushes every work item in the given queue
```c
void flush_workqueue(struct workqueue_struct *queue)
```

- A workqueue can be destroted with `destroy_workqueue()`
```c
void destroy_workqueue(struct workqueue_struct *queue)
```
##### Global Kernel Workqueue
In most situations, your code does not necessarily need the performance of its own dedicated set of threads. Instead, you may want to use kernel shared queue, which already has its set of kernel threads pre-allocated for running works. This kernel-global workqueue is the so-called `system_wq` workqueue, defined in `kernel/workqueue.c`. There is actually one instance per CPU, each backed by a dedicated thread named `events/n`, where `n` is the processor number (or index) to which the thread is bound.

You can queue a work item in the default system workqueue using one of the following functions:
```c
int schedule_work(struct work_struct *work);
int schedule_delayed_work(struct delayed_work *dwork,
						  unsigned long delay);
int schedule_work_on(int cpu, struct work_struct *work);
int schedule_delayed_work_on(int cpu,
							 struct delayed_work *dwork,
							 unsigned long delay);
```

Since this system workqueue is shared, you should not queue works which can run for too long, otherwise it may slow down other contender works, which could then wait for longer than they should before being executed.

In order to flush the kernel-global workqueue—that is, ensure a given batch of work is completed—we can use `flush_scheduled_work()`
```c
void flush_scheduled_work(void);
```
##### Concurrency Managed Workqueue (cmwq)
The original workqueue implementation used two kinds of workqueues: those with a single thread system-wide, and those with a thread per CPU. For a growing number of CPUs, this led to some limitations:
- On very large systems, the kernel could run out of process identifiers (PIDs) (defaulting to 32,000) just at the boot, even before the `init` process started.
- Multithreaded workqueues provided poor concurrency management as their threads compete for the CPU with each other threads on the system. As there were more CPU contenders, this introduced some overhead—that is, more context switches than necessary.
- The consumption of far more resources than were really needed.

The upgraded workqueue, called the concurrency managed workqueue (cmwq) uses per-CPU worker pools shared by all workqueues in order to automatically provide a dynamic and flexible level of concurrency, abstracting such details for the API users.

Using cmwq:
- Workqueues can be created using `alloc_workqueue()` and `alloc_ordered_workqueue()`. They both allocate a workqueue and return a pointer to it on success, and `NULL` on failure. 
```c
struct workqueue_struct *alloc_workqueue(const char *fmt,
										 unsigned int flags,
										 int max_active, ...);
#define alloc_ordered_workqueue(fmt, flags, args...) [...]
```
`alloc_workqueue()` creates a workqueue based on `max_active`, which defines the concurrency level by limiting the number of works that can be executing simultaneously from this workqueue on any given CPU. On the other hand, `alloc_ordered_ workqueue()` creates a workqueue that processes each work item one by one in queued order (that is, first-in, first-out (FIFO) order).

`flags` controls how and when work items are queued, assigned execution resources, scheduled, and executed.
	- `WQ_UNBOUND`: Legacy workqueues had a worker thread per CPU and were designed to run tasks on the CPU where they were submitted. The kernel scheduler had no choice but to always schedule a worker on the CPU on which it was defined. With this approach, even a single workqueue was able to prevent a CPU from idling and being turned off, which leads to increased power consumption or poor scheduling policies. WQ_UNBOUND turns off the previously-described behavior. Work items are not bound to the CPU anymore, hence the name unbound workqueues. There is no more locality, and the scheduler can reschedule workers on any CPU as it sees fit. The scheduler has the last word now and can balance CPU load, especially for long and sometimes CPU-intensive works.
	- `WQ_MEM_RECLAIM`: This flag is to be set for workqueues that need to guarantee forward progress during the memory reclaim path (when free memory is running dangerously low; the system is said to be under memory pressure). In this case, GFP_KERNEL allocations may block and deadlock the entire workqueue. The workqueue is then guaranteed to have at least a ready-to-use worker thread—a so-called rescuer thread—reserved for it, regardless of memory pressure, so that it can progress forward. There's one rescuer thread allocated for each workqueue.
	- `WQ_FREEZABLE`: This flag is used for power-management purposes. A workqueue with this flag set will be frozen when the system is suspended or hibernates. On the freezing path, all current work items of the worker(s) will be processed. When the freeze is complete, no new work item will be executed until the system is unfrozen. Filesystem-related workqueue(s) may use this flag (that is, to ensure that modifications made on files are pushed to the disk or create a hibernation image on the freezing path and that no modification is made on-disk after the hibernation image has been created. In this situation, non-freezable items or doing things differently could lead to filesystem corruption.
	- `WQ_HIHGPRI`: Tasks with this flag set run immediately and do not wait for the CPU to become available. This flag is used for workqueues that queue work items requiring a high priority for execution. Such workqueues have worker threads with a high priority level.
	- `WQ_CPU_INTENSIVE`: Work items of CPU-intensive workqueues may burn a lot of CPU cycles and do not participate in workqueue concurrency management. Instead, as with any other task, their execution is regulated by the system scheduler, which makes this flag handy for bound work items that may consume a lot of CPU time.
 
- The returned workqueue can be freed using the `destroy_workqueue()` function.
```c
void destroy_workqueue(struct workqueue_struct *wq);
```