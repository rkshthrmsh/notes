## Recoverable Errors with `Result`
- `Resul` enum has two variants: 
```rust
enum Result<T, E> {
	Ok(T),  // T -> type of value in a success case
	Err(E), // E -> type of error in a failure case
}
```

- Example of using `Result` variants with a `match`.
```rust
use std::fs::File;
use std::io::ErrorKind;

fn main() {
	let greeting_file_result = File::open("hello.txt");

	let greeting_file = match greeting_file_result {
		Ok(file) => file,
		Err(error) => match error.kind() {
			ErrorKind::NotFound => match File::create("hello.txt") {
				Ok(fc) => fc,
				Err(e) => panic!("Problem creating file: {:?}", e),
			},
			other_err => panic!("Problem opening file: {:?}", other_err);
		},
	};
}
```

- `Result` has many helper functions to achieve the same functionality as the above code
	- `unwrap()` will return the value inside `Ok` if the result is `Ok`, else it will call the `panic!` macro.
	- `expect()` is similar to `unwrap`, but let's the user choose the panic message. Therefore, this method is preferable.
```rust
let greeting_file = File::open("hello.txt").unwrap();
let greeting_file = File::open("hello.txt").expect("Where's the file?");
```

### Propagating Errors
- Propagating errors, wherein a function returns the error to the calling code, is a common practice that gives more control to the caller.
- `Result` can be used to propagate errors. The following code is an example of opening a file and reading the contents.
```rust
use std::fs::File;
use std::io::{self, Read};

fn read_username_from_file() -> Result<String, io::Error> {
	let username_file_result = File::open("hello.txt");
	let mut username_file = match username_file_result {
		Ok(file) => file, 
		Err(e) => return Err(e),
	};

	let mut username = String::new();
	match username_file.read_to_string(&mut username) {
		Ok(_) => Ok(username),
		Err(e) => Err(e),
	}
}
```

- The pattern of propagating errors is so common that Rust provides a useful shortcut for this: `?`.
```rust
fn read_username_from_file() -> Result<String, io::Error> {
	let mut username_file = File::open("hello.txt")?;
	let mut username = String::new();
	username_file.read_to_string(&mut username)?;
	Ok(username)
}
```
- The `?` is different from `match` because the error values that have the `?` operator called on them go through the `from` function which converts values from one type to another. This ensures that the error type received is converted to the error type specified in the function's return type.
- Further simplification:
```rust
fn read_username_from_file() -> Result<String, io::Error> {
	let mut username = String::new();
	File::open("hello.txt")?.read_to_string(&mut username)?;
	Ok(username)
}
```

- Finally, reading a file into a string is such a common operation that the standard library provides a convenient. `fs::read_to_string` function to open the file, create the string, read the contents, put the contents into a string, and return it.
```rust
fn_read_username_from_file() -> Result<String, io::Error> {
	fs::read_to_string("hello.txt")
}
```

- The behavior of the `?` operator when called on an `Option<T>` is similar to its behavior when called on a `Result<T, E>`: if the value is `None`, the `None` will be returned early from the function at that point. If the value is `Some`, the value inside the `Some` is the resulting value of the expression and the function continues. Listing 9-11 has an example of a function that finds the last character of the first line in the given text:
- The `?` operator won’t automatically convert a `Result` to an `Option` or vice versa; in those cases, you can use methods like the `ok` method on `Result` or the `ok_or` method on `Option` to do the conversion explicitly.

- Although `main` is a special function that can only return `()`, it can be made to return errors using the return type `Result<(), E>`.
```rust
use std::error::Error;
use std::fs::File;

fn main() -> Result<(), Box<dyn Error>> { // include "any kind of error in return type"
	let greeting_file = File::open("hello.txt")?;
	Ok(())
}
```
- When a `main` function returns `Result<(), E>`, the executable will exit with value `0` if `Ok(())` is returned and a nonzero value otherwise.