# Error Handling
#rust 

- Rust groups errors into two categories: *recoverable* and *unrecoverable* errors.
- Rust provides `Result<T, E>` for recoverable errors and the `panic!` macro to stop execution for unrecoverable errors.

## Unrecoverable Errors with `panic!`
- There are two ways to cause a panic in practice: by taking an action that causes our code to panic (such as accessing an array past the end) or by explicitly calling the `panic!` macro.
```rust
fn main() {
	panic!("crash and burn");
}
```
- By default, these panics will print a failure message, unwind, clean up the stack, and quit. However, unwinding is a lot of work. Therefore, Rust allows you to choose an alternative of immediately aborting, which means that the memory used by the program will then need to be cleaned up by the OS.
```toml
# Cargo.toml
[profile.release]
panic = 'abort'
```

- Panic backtraces can be obtained by setting the environment variable `RUST_BACKTRACE` to anything except 0.

## [[Recoverable Errors with `Result`]]

## To `panic!` or Not to `panic!`
- When choosing between whether to `panic!` or return `Result`, the latter is a good default choice for a function that might fail because this gives the calling code an opportunity to recover.
- However, examples, prototype code, and tests are situations in which it is better to panic.
- [Guidelines for Error Handling](https://rust-book.cs.brown.edu/ch09-03-to-panic-or-not-to-panic.html#guidelines-for-error-handling)
- As a programmer, one could also create custom types to validation. An example of a custom type to ensure an input number is between 1..100 is shown here.
```rust 
pub struct Guess {
	value: i32,
}
impl Guess {
	pub fn new(value: i32) -> Guess {
		if value < 1 || value > 100 {
			panic!("Value should be between 1 and 100");
		}
		Guess { value }
	}

	pub fn value(&self) -> i32 {
		self.value
	}
}
```