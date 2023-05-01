# Multiprocessing in Python
#python 

- A process refers to any computer program. In Python, each process is a separate instantiation of the Python interpreter and has one default thread called the main thread. 
- Python provides the `multiprocessing` module for launching processes that run in parallel. However parallel execution is not always guaranteed and is determined by the underlying hardware and the nature of the program itself.
- "A process in Python is represented as an instance of the `multiprocessing.Process` class. Once a new process is created the Python runtime will interface with the underlying OS and request that a new native process be generated. `mutliprocessing.Process` then provides a Python-based reference to the underlying process." [1]

```python
import multiprocessing

# Create a process
process = multiprocessing.Process(target=<function>, args=[arg1, arg2])

# Running a process: start() does not block, i.e., returns immediately
process.start() # or process.run()

# Wait for process to finish
process.join()

# Forcefully terminating a process
process.terminate() # or process.kill()
```

- The lifecycle of a typical process involves creation, execution, and termination [2].
	- Creation involves making an instance object of the `multiprocessing.Process` class or one of its child classes. The first instance is created from the Python default process, also known as the `MainProcess`. New processes can be created using fork, spawn, or fork server techniques [3]. 
		- Fork: Create a copy (or fork) of the parent process.
		- Spawn: Create a new instance of the Python interpreter.
		- Fork Server: Create a copy of the parent process and use it as a template to create all future child processes.
	- Execution is done by calling the `run()` or `start()` methods.
	- Termination occurs when:
		- All non-daemon child processes have terminated, including the main process.
		- All non-daemon threads have terminated, including the main thread.
		- Processes can also be terminated forcefully by calling the `terminate()` or `kill()` methods.

## Sharing Values between Processes
- Processes can share values between each other by using the `multiprocessing.Value` and `multiprocessing.Array` classes.
- In addition, the `Pipe` and `Queue` classes can be used to create objects for sharing messages directly between processes.
```python
import multiprocessing

# Create a pipe
conn1, conn2 = multiprocessing.Pipe(). # Creates two Connection objects

# Send an object
conn2.send('Hello World')

# Receive an object
object = conn1.recv()
```

```python
# Create a queue
queue = mutliprocessing.Queue()

# Add an item to queue
queue.put(item)

# Retreive from queue
item = queue.get()
```

## Process Start Methods
- Use the `get_all_start_methods()` to view start methods supported by the OS, and `get_start_method()` to check the currently configured start method. 
- `set_start_method()` can be used to configure the start method.
- Best practices:
	- Set the start method first, prior to all other code.
	- Set start method only once in a program.
	- Set start method within a protected entry point such as,
	  `if __name__ == "__main__"`

## Process Instance Attributes
```python
from multiprocessing import Process

if __name__ == "__main__":
	process = Process()

	# Get process name. A unique name is created for each process.
	process.name

	# Retreive whether process is a daemon or not.
	# Child processes get the same daemon flag as parent process.
	# MainProcess has daemon set to False by default.
	process.daemon

	# Query process ID (PID). PID is assigned only at process start.
	process.start()
	process.pid

	# Check if process is running.
	process.is_alive()

	# Report process exit code
	process.exitcode
```

## Utilities
- `active_children()` retrieves a list of all active child processes.
- `cpu_count()` provides the number of CPUs available on the system.
- `current_process()` returns the currently active process.
- `parent_process()` returns parent process, if any.

## [[Process and Thread Synchronization|Process Synchronization]] 

## References
[1] https://superfastpython.com/multiprocessing-in-python/
[2] https://superfastpython.com/process-life-cycle/
[3] https://superfastpython.com/parent-process-vs-child-process-in-python/