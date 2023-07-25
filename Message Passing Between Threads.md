## Using Message Passing to Transfer Data Between Threads
- Message passing is an approach to threading where threads communicate by sending each other messages containing data.
- To accomplish this, Rust's standard library provides an implementation of channels. 
- A channel is a programming concept by which data is sent from one thread to another. It has two halves: a transmitter and a receiver.
- Channels can be created using the standard library's `mpsc` implementation. `mpsc` stands for multiple producer, single consumer.
- `mpsc::channel()` returns a tuple, with the first element being the transmitting end and the second one being the receiving end.
- NOTE: Channels can only send data of a single type. If multiple types need to be sent, use an enum or the `Any` trait.
```rust
use std::sync::mpsc:

fn main() {
	let (tx, rx) = mpsc::channel();

	thread::spawn(move || { // move tx end to a new thread.
		let vale = String::from("hi");
		tx.send(val).unwrap(); // send returns Err if rx was dropped.
	});

	let received = rx.recv().unwrap();
	println!("Got: {}", received);
}
```
- The `send` function on the transmitting end takes ownership of its argument, and when the value is moved, the receiving end takes ownership of it. This implies that the sent values cannot be reused in the transmitting thread.
- The receiving end has two methods for receiving the data:
	- `recv` which blocks the main thread's execution till a value is sent down the channel.
	- `try_recv` which does not block, but will return `Result<T, E>` immediately. This method is useful if the thread calling it has other work to do while waiting for messages.
 - The receiving end can also be treated as an iterator in a loop:
```rust
use std::sync::mpsc;
use std::thread;
use std::time::Duration;

fn main() {
	let (tx, rx) = mpsc::channel();
	thread::spawn(move || {
		let vals = vec![
			String::from("hi"),
			String::from("from"),
			String::from("the"),
			String::from("thread"),
		];
		
		for val in vals {
			tx.send(val).unwrap();
			thread::sleep(Duration::from_secs(1));
		}
	});
	for received in rx { // using receiving end as an iterator.
		println!("Got: {}", received);
	}
}
```

- Finally, multiple transmitting ends can be used to send data to a single receiving end. These transmitting ends can be created by cloning the original transmitting end created by the call to `mpsc::channel()`. For example:
```rust
let (tx, rx) = mpsc::channel();
let tx1 = tx.clone();
```