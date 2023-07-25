## Unsafe Rust
- Unsafe Rust is like a hidden language inside Rust that does not enforce memory safety guarantees.
- It exists because, by nature, static analysis is conservative. Moreover, the underlying hardware is inherently unsafe. Therefore, interacting with this hardware requires unsafe Rust.

### Unsafe Superpowers
- Switching to unsafe Rust can be done by prepending a block with the `unsafe` keyword.
- There are five actions that can be done in unsafe mode which are not possible in safe Rust. These are:
	- Dereference a raw pointer.
	- Call an unsafe function or method.
	- Access or modify a mutable static variable.
	- Implement an unsafe trait
	- Access fields of `union`s.
- Unsafe does not turn off the borrow checker or disable Rust's safety features. References in unsafe code will still be checked.
- Keeping `unsafe` blocks small makes it easy to debug any memory related bugs.

#### Dereferencing a Raw Pointer
- Like references raw pointers can be immutable or mutable and are written as `*const T` and `*mut T`, respectively.
- Differences with references and smart pointers:
	- Raw pointers are allowed to have both immutable and mutable pointers, or multiple mutable pointers to the same location. They ignore the borrowing rules.
	- No guarantee of pointing to valid memory.
	- Can be null.
	- No automatic cleanup.
- This represents a tradeoff in safety in exchange for greater performance or the ability to interface with another language or hardware.
```rust
let mut num = 5;
let r1 = &num as *const i32; // raw pointers can be created in safe code.
let r2 = &mut num as *mut i32;

unsafe { // raw pointers can only be dereferenced in an unsafe block.
	println!("r1 is: {}", *r1);
	println!("r2 is: {}", *r2);
}
```
- One major use case for raw pointers is when interfacing with C code. Another case is when building up safe abstractions that the borrow checker doesn’t understand.“

#### Calling an Unsafe Function or Method
```rust
unsafe fn dangerous() {}

unsafe { // ensures user has read the documentation for dangerous.
	dangerous();
}
```
- It is not always necessary to have the function be `unsafe`. Instead, `unsafe` code can be wrapped inside a safe function. The `split_at_mut` method on slices provides such an implementation. The following is an instructional example on its implementation.
```rust
use std::slice;
fn split_at_mut(values: &mut [i32], mid: usize) -> (&mut [i32], &mut [i32]) -> {
	let len = values.len();
	let ptr = values.as_mut_ptr(): // returns a raw pointer *mut i32

	assert!(mid <= len);

	unsafe {
		(
			slice::from_raw_parts_mut(ptr, mid),
			slice::from_raw_parts_mut(ptr.add(mid), len - mid),
		)
	}
}
```

##### Using `extern` Functions to Call External Code
- Rust provides the `extern` keyword to interact with code written in another language.
- The use of `extern` is facilitated by the Foreign Function Interface, which is a way for a programming language to define function and enable a different programming language to call those functions.
- Functions defined with `extern` are always unsafe because they do not enforce Rust's rules and guarantees.
```rust
extern "C" { // application binary interface
	fn abs(input: i32) -> i32; // C function signature
}

fn main() {
	unsafe {
		println!("Absolute value according to C: {}", abs(-3));
	}
}
```

- The `extern` keyword can also be used to create an interface that allows other languages to call Rust functions.
```rust
$[no_mangle] // prevents compiler from mangling function names
pub extern "C" fn call_from_c() {
	println!("Just called a Rust function from C!");
}
```

#### Accessing or Modifying a Mutable Static Variable
- In Rust global variables are called static variables.
- The names of static variables are in `SCREAMING_SNAKE_CASE` by convention.
```rust
static HELLO_WORLD: &str = "Hello, world!";
fn main() {
	println!("name is: {}", HELLOW_WORLD);
}
```
- Static variables are similar to constants and accessing an immutable static variable is safe.
- A subtle difference between constants and immutable static variables is that values in a static variable have a fixed address in memory. Using the value will always access the same data. Constants, on the other hand, are allowed to duplicate their data whenever they’re used.
- Another difference is that static variables can be mutable. Accessing and modifying mutable static variables is _unsafe_.
```rust
static mut COUNTER: u32 = 0;
fn add_to_count(inc: u32) {
	unsafe {
		COUTNER += inc;
	}
}

fn main() {
	add_to_count(3);
	unsafe {
		println!("{}", COUNTER):
	}
}
```

#### Implementing an Unsafe Trait
- We declare that a trait is `unsafe` by adding the `unsafe` keyword before `trait` and marking the implementation of the trait as `unsafe` too.
```rust
unsafe trait Foo {
	 // methods
}

unsafe impl Foo for i32 {
	// method implementations
}
```

#### Accessing Fields of a Union
- The final action that works only with `unsafe` is accessing fields of a _union_.
- A `union` is similar to a `struct`, but only one declared field is used in a particular instance at one time. Unions are primarily used to interface with unions in C code.
- Accessing union fields is unsafe because Rust can’t guarantee the type of the data currently being stored in the union instance.