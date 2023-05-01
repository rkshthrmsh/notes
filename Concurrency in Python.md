# Concurrency in Python
#python 

- Concurrency refers to the simultaneous execution of a program.
- Concurrency can be achieved at the task, thread, or process level. Parallelism is a subset of concurrency wherein multiple processes are run simultaneously.
- Python provides three modules for task-, thread-, and process-level concurrency. These are the `asyncio`, `threading`, and `multiprocessing` modules respectively.
- `asyncio` uses cooperative multitasking, i.e., the tasks actively cooperate by indicating when they are ready to be switched out. At that point, they give up control to an event loop that handles the coordination of other tasks. The event loop, however, cannot break in to a task to regain control.
- On the other hand, in pre-emptive multitasking using [[Threading in Python|threading]], the OS is in control of the threads and can choose to switch tasks at any point, even in the middle of trivial statements such as `x = x + 1`. This makes it easy to write code that can be multi-threaded, but care needs to be taken to ensure that the code is thread-safe.
- Finally, `multiprocessing` can create multiple instances of the Python interpreter on separate processor cores for running the program in parallel. Bringing up a new instance of the interpreter is a heavyweight operation which is much slower than creating a new thread. 
- Concurrency is useful in two situations--CPU-bound and IO-bound operations.
	- CPU-bound operations execute significant computation without communicating to the network or accessing files (IO). Since such operations have minimal waiting periods, they stand to improve performance by increasing parallel execution, such as through multiprocessing.
	- IO-bound operations are those that rely on inputs and outputs for executing a part of the program. Typically, the amount of time waiting for IO operations is much larger than that for CPU based operations. Therefore such programs would gain significant performance boosts from task or thread level concurrency.
- A summary of the concurrency types is captured in the table below:
| Concurrency Type                            | Switching Decision                                                | No. of Processors |
| ------------------------------------------- | ----------------------------------------------------------------- | ----------------- |
| Pre-emptive multitasking <br/>(`threading`) | The OS decides when to switch tasks. Program has limited control. | 1                 |
| Cooperative multitasking <br/>(`asyncio`)   | The task decides when to give up control.                         | 1                 |
| Multiprocessing <br/>(`multiprocessing`)    | The processes run simultaneously on different processors.         | Multiple          | 

## References
[1] https://realpython.com/python-concurrency/#when-is-concurrency-useful