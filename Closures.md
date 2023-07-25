## Closures: Anonymous Functions that Capture Their Environment
- Rust closures are anonymous functions you can save in a variable or pass as arguments to other functions.
- Unlike functions, 
	- closures can capture values from the scope in which they are defined.
	- closures do not usually require type annotations for the parameters and return values.
```rust
fn  add_one_fn   (x: u32) -> u32 { x + 1 }
let add_one_cl = |x: u32| -> u32 { x + 1 }; // fully annotated closure
let add_one_cl = |x|      ->     { x + 1 }; // concise closure
let add_one_cl = |x|      ->       x + 1  ; // most concise closure
```

- For closure definitions, the compiler will infer one concrete type for each of the parameters and the return value. This inference happens the first time the compiler encounters a call to the closure. Any attempt to use the closure with a different data type later will cause errors.
```rust
let example_closure = |x| x;
let s = example_closure(String::from("hello")); // compiler infers closure types
let n = example_closure(5); // will cause error
```

### Capturing the Environment with Closures
- Closures can capture values from their environment in three ways, which directly map to the three ways a function can take a parameter: borrowing immutably, borrowing mutably, and taking ownership. 
- The closure will decide which of these to use based on what the body of the function does with the captured values.
- If you want to force the closure to take ownership of the values it uses in the environment even though the body of the closure doesn’t strictly need ownership, you can use the `move` keyword before the parameter list.
- This technique is mostly useful when passing a closure to a new thread to move the data so that it’s owned by the new thread.
```rust
use std::thread;

fn main() {
	let list = vec![1, 3, 4];
	println!("Before defining closure: {:?}", list);

	thread::spawn(move || println!("From thread: {:?}", list)) // force move
		.join()
		.unwrap();
}
```

### Moving Captured Values Out of Closures and the `Fn` Traits
- A closure body can do one of the following:
	- Move a captured value out of the closure.
	- Mutate the captured value.
	- Neither move nor mutate the value.
	- Capture nothing from the environment.
- The way a closure captures and handles environment values determines which traits the closure implements. Any closure can implement one or multiple of three following traits:
	- `FnOnce` applies to closures that can be called once. All closures implement this trait because they can be called. A closure that moves captured values out of its body will only implement `FnOnce` trait because it can only be called once.
	- `FnMut` applies to closures that don't move captured values out of the body, but might mutate the captured values. These closures can be called more than once.
	- `Fn` applies to closures that don't move nor mutate captured values, as well as closures that don't capture anything from the environment.
- The following is the definition of the standard library's `unwrap_or_else` method on `Option<T>`
```rust
impl<T> Option<T> {
	pub fn unwrap_or_else<F>(self, f:F) -> T
	where
		F: FnOnce() -> T // Trait of the closure: one-call function returning T
	{
		match self {
			Some(x) => x,
			None => f()
		}
	}
}
```

### Closures Must Name Captured Lifetimes
- When defining functions that accept or return closures, it is important to think about the lifetime of the data captured by the closures. 
![[Pasted image 20230614163804.png]]
- In the example above, Rust needs to know that the closure returned by the `make_a_cloner` function need only live as long as `s_ref`. This can be done explicitly by the following syntax. This lets the Rust compiler know that the returned closure must live no longer than lifetime `'a`, which is also the lifetime of `s_ref`.
```rust
fn make_a_cloner<'a>(s_ref: &'a str) -> impl Fn() -> String + 'a {}
```