## Shared-State Concurrency
- Mutex, or mutual exclusion, can be used to access data from one thread at a time. To access the data in a mutex, a thread must first acquire the mutex's lock.
- Working with mutexes requires following two rules:
	- A lock must be acquired before using the data.
	- The data must be unlocked at the end of an operation so that other threads can acquire the lock.
- Rust's type system makes implementing both of these rules much simpler.
- Moreover, multiple ownership, as required by the `move` of a value into newly spawned threads requires an `atomic` smart pointer. Non-atomic types such as the `Rc<T>` pointer do not use any concurrency primitives to avoid race conditions. Therefore, a type like `Arc<T>` is required.
- The following is an example that ties the concepts of a `Mutex` and `Arc<T>` together to spawn threads, increment a common value, and print it out at the end.
```rust
use std::sync::{Arc, Mutex};
use std::thread;

fn main() {
	let counter = Arc::new(Mutex::new(0));
	let mut handles = vec![];
	for _ in 0..10 {
		let counter = Arc::clone(&counter);
		let handle = thread::spawn(move || {
			let mut num = counter.lock().unwrap();
			// lock for acquiring mutex data and unwrap for the Result type which is returned
			*num += 1;
		});
		handles.push(handle);
	}

	for handle in handles {
		handle.join().unwrap();
	}

	println!("Result: {}", *counter.lock().unwrap());
}
```

- Note that if you are doing simple numerical operations, there are types simpler than `Mutex<T>` types provided by the [`std::sync::atomic` module of the standard library](https://doc.rust-lang.org/std/sync/atomic/index.html).

- `Mutex<T>` provides interior mutability, as the `Cell` family does. And, like `Rc<T>` has the risk of creating reference cycles, `Mutex<T>` comes with the risk of creating deadlocks.