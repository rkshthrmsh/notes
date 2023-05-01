## Process and Thread Synchronization
#python 

When multiple processes / threads each access the same data it is necessary to ensure proper synchronization to avoid race conditions. There are multiple techniques to avoid race conditions, each of which are described below. It is important to understand that each technique has its own use case.
- **Lock or mutex lock**: `multiprocessing` and `threading` provide a `Lock` class which ensures that only one process / thread is executing at any given time. In the presence of a lock a process / thread needs to acquire the lock (hence called being in a locked state) to continue execution. Other 'unlocked' processes / threads will need to wait for the lock to be released. A `Lock` can be `acquired()` and `released()`. However, using a context manager is recommended as a safe practice to avoid deadlock conditions.

- **Reentrant Lock**: A reentrant lock is similar to a mutex lock, except that it allows a process / thread to acquire the same lock more than once. This is useful when closely connected, critical pieces of code (context managed with a lock) are spread across multiple functions many of which may be called hierarchically. If a process / thread is restricted from executing a hierarchically nested critical function because the lock is held by the calling function, then a deadlock occurs. A reentrant lock allows the process / thread to execute the called function, with proper lock semantics that must be adhered to recursively (`acquire()` & `release()`, or context manager), and returns to the caller.

- **Condition Variable**: A condition variable can be used to synchronize multiple waiting processes / threads based on a notification. Similar to a `Lock`, the `Condition` object can be acquired by a process / thread after which the process / thread can choose to wait for a notification from another process / thread. Another process / thread can use the same `Condition` object to notify waiting processes / threads. The following is an example with `multiprocessing` which is equally applicable to `threading`.
```python
import multiprocessing

# Create Condition object
condition = multiprocessing.Condition()

# Acquire condition and wait to be notified
with condition:
	condition.wait(timeout=<wait_time>)
	condition.wait_for(<function_returning_boolean>)

# Acquire condition and notify
with condition:
	condition.notify()
	condition.notify_all()
```

- **Semaphore**: A semaphore is a more general extension of the `Lock`. Where the `Lock` is limited to one acquire and release, a `Semphore` supports multiple, but limited, access to a resource. It is a counter with a mutex lock. Conversely, a mutex lock can be thought of as a Semaphore with count one.

- **Event**: The `Event` class makes it easy for processes / threads to share a boolean variable between each other. Processes / threads sharing an event object can check if the event is set, set the event, clear the event, or wait for the event to be set. The following example is based on `multiprocessing` but equally applicable to `threading`.
 
```python
# Create an Event
event = multiprocessing.Event()

# Check if event is set
if event.is_set():
	...

# Set the event
event.set()

# Mark the event as not set
event.clear()

# Wait for event
event.wait()
```

- **Barrier**: A barrier allows multiple processes / threads to wait on the same `Barrier` object until a predefined fixed number of processes arrive, after which all processes are notified and released to continue execution. This is useful for initializing processes / threads before releasing them.
```python
...
# Create a barrier
barrier = Barrier(n) # n is number of processes / threads to wait for

# Wait on barrier
barrier.wait() # returns an integer indicating the no. of processes / threads yet to arrive

# Abort
barrier.abort()

# Reset
barrier.reset()
```