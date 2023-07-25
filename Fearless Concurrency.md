# Fearless Concurrency
#rust 

## Using Threads to Run Code Simultaneously
- Threads can be used to split computation and run multiple tasks at the same time. Although this improves performance, it adds to complexity.
- Problems with threading include:
	- Race conditions: Wherein threads attempt to access data in an inconsistent order.
	- Deadlocks: Where two threads wait for each other with neither of them completing operation.
	- Inconsistent bugs


### Creating a New Thread with `spawn`
- To create a new thread we call the `thread::spawn` function and pass it a closure.
- By default, when the main thread of a program completes, all spawned. threads are shut down, whether or not they have finished running.
- The return type of `thread::spawn` is `JoinHandle`, which when we call the `join` method on it, will wait for its thread to finish. Therefore, the `JoinHandle` from spawned threads can be `join`ed to ensure that the threads don't end prematurely.
```rust
use std::thread;
use std::time::Duration;

fn main() {
	let handle = thread::spawn(|| {
		for i in 1..10 {
			println!("{} from spawnded thread");
		}
	});

	for i in 1..5 {
		println!("{} from the main thread");
		thread::sleep(Duraton::from_millis(1));
	}

	handle.join().unwrap();
}
```

- Often, it is necessary for the spawned thread to take ownership of the value from its environment. For this, the closure passed to `spawn` can be defined with `move`, which transfers ownership of the required variables.

## [[Message Passing Between Threads|Using Message Passing to Transfer Data Between Threads]]

## [[Shared-State Concurrency]]

## Extensible Concurrency with the `Sync` and `Send` Traits
- Rust has very few concurrency features that are part of the core language. However, two concurrency concepts are embedded in the language: the `std::marker` traits `Sync` and `Send`.
- The `Send` marker trait indicates that the ownership of values of the type implementing `Send` can be transferred between threads.
- Almost every Rust type is `Send`. Any type composed entirely of `Send` types is automatically marked as `Send` as well.
- One exception that was covered in the previous section is `Rc<T>`.

- The `Sync` marker trait indicates that it is safe for the type implementing `Sync` to be referenced from multiple threads. 
- Any type `T` is `Sync` if `&T` (an immutable reference to `T`) is `Send`, meaning the reference can be sent safely to another thread. 
- Similar to `Send`, primitive types are `Sync`, and types composed entirely of types that are `Sync` are also `Sync`.

- The reason for having separate `Send` and `Sync` traits is that a type can sometimes be one, both, or neither.
	- The smart pointer `Rc<T>` is also neither `Send` nor `Sync`, for reasons described above.
	- The `RefCell<T>` type and the family of related `Cell<T>` types are `Send` (if `T: Send`), but they are not `Sync`. A `RefCell` can be sent across a thread boundary, but not accessed concurrently because the implementation of borrow checking that `RefCell<T>` does at runtime is not thread-safe.
	- The smart pointer `Mutex<T>` is `Send` and `Sync`, and can be used to share access with multiple threads.
	- The type `mutexguard<'a, t>` that is returned by `mutex::lock` is `sync` (if `T: Sync`) but not `Send`. It is specifically not `Send` because [some platforms mandate that mutexes are unlocked by the same thread that locked them](https://github.com/rust-lang/rust/issues/23465#issuecomment-82730326).