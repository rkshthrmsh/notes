# Threading in Python
#python 

- A thread is a concurrent flow of execution in a process. A process maybe composed of multiple threads. However, it is important to understand that threads of a process will only be running one at a time.
- Tasks that spend time waiting for external events (IOs) are a good example of candidates for threading.
- The standard Python library contains the `threading` module which provides primitives for creating and managing threads. The following code is an example of starting a thread in Python:
```python
import threading 
import time

def thread_function(param1, param2):
	time.sleep(2)

if __name__ = "__main__":
	# Create a thread named t
	t = threading.Thread(target=thread_function, args=[arg1, arg2])
	# Start a thread named t
	t.start()
```

## Daemon Threads
- Daemon is a process that runs in the background.
- A daemon will shut down immediately when the program exits. On the other hand, a user thread holds up the program, i.e., the program cannot be shutdown when a user thread is running.
```python
# Creating a daemon thread
dae = threading.Thread(target=thread_function, args=[arg1, arg2], daemon=True)
```


- Using `Thread.join()` tells one thread to wait for the finish of another thread.

## Working with Multiple Threads
- `ThreadPoolExecutor` from the `concurrent.futures` library can be used with the context manager to manage the creation and destruction of a thread pool.
```python
import concurrent.futures

# Insert thread_function from examples above

if __name__ = "__main__":
	with concurrent.futures.ThreadPoolExecutor(max_workers=3) as executor:
		executor.map(thread_function, range(3))
```
- `max_workers` signifies the maximum number of threads in the pool.
- Using `with` ensures that all threads are joined with `.join()` before program exit.
- Another method to start a thread using the `ThreadPoolExecutor` is by using `.submit()`:
```python
if __name__ = "__main__":
	with concurrent.futures.ThreadPoolExecutor(max_workers=3) as executor:
		executor.submit(thread_function, *args, **kwargs)
```

## Synchronization Using `Lock`
- "A `Lock` is an object that acts like a hall pass. Only one thread at a time can have the `Lock`" [1]. Threads without the `Lock` need to wait until the current owner gives it up.
- `.acquire()` and `.release()` are two function that can be used for this purpose.
- While working with locks care needs to be taken to ensure that there are no deadlocks in the program. Deadlocks could occur due to:
	- "An implementation bug where a `Lock` is not released properly.
	- A design issue where a utility function needs to be called by functions that might or might. not already have the lock" [1]. `RLock` can help in this situation.
- Apart from deadlocks, race conditions could lead to unexpected results. It is important to understand how threads access common resources and the execution of threading methods (`.release()`, `.acquire()`, etc.) to ensure that the program is designed to be free of race conditions.

## Synchronization Using `Queue`
- `threading.Event` object can be used to signal an event to multiple threads that are waiting on that event. However, threads that are waiting for the event need not stop running in the meantime.
- Using `Queue` for synchronization has a few advantages:
	- It allows pipelining of more than one data point between multiple threads.
	- Managing of the acquire and release of locks is abstracted away into simpler `.get()` and `.put()` methods. This ensures that `Queue` is thread-safe.
 
```python
import concurrent.futures
import queue
import random
import threading
import time

def producer(queue, event):
    """Pretend we're getting a number from the network."""
    while not event.is_set():
        message = random.randint(1, 101)
        queue.put(message)


def consumer(queue, event):
    """Pretend we're saving a number in the database."""
    while not event.is_set() or not queue.empty():
        message = queue.get()


if __name__ == "__main__":
	# Create Queue object
    pipeline = queue.Queue(maxsize=10)
	# Create Event object
    event = threading.Event()
    with concurrent.futures.ThreadPoolExecutor(max_workers=2) as executor:
        executor.submit(producer, pipeline, event)
        executor.submit(consumer, pipeline, event)

        time.sleep(0.1)
        event.set()
```

- Apart from `Lock` and `Queue`, the `threading` module provides a few more primitives for solving concurrency issues:
	- `Semaphore`: A `Sempahore` is a counter which is typically used to protect a feature with limited capacity. For example, to control access to a particular compute-limited resource.
	- `Timer`: This primitive provides a way to schedule a function to be called after a certain amount of time has passed.
	- `Barrier`: `Barrier` can be used to keep a fixed number of threads in sync. This is useful in situations where multiple threads need to be initialized before any of them starts executing. Using `Barrier`, all threads will remain blocked until all are released at the same time. Note, however, that although threads are released simultaneously, only one thread, as determined by the OS, will be scheduled to start execution after the release.
Refer to [[Process and Thread Synchronization|synchronization]] for more on information on these techniques.

## References
[1] https://realpython.com/intro-to-python-threading/