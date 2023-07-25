## How to Write Tests
- The bodies of test functions typically perform three actions:
	- Set up any needed data or state.
	- Run the code you want to test.
	- Assert the results are what you expect.
- To change a function into a test function, `#[test]` needs to be added on the line before the `fn` definition.
- Whenever a new library project is created, Rust automatically creates a test module with a test function in it.
- Rust also provide benchmark testing which is worth exploring. See [the documentation about benchmark tests](https://doc.rust-lang.org/unstable-book/library-features/test.html).

### Checking Result Values 
- Rust provides different macros for evaluating the results of a test.
	- The `assert!` macro ensures that some condition in a test evaluates to `true`.
	- The `assert_eq!` and `assert_ne!` macros compare two arguments for equality or inequality, respectively. They will also print the two values if the assertion fails. These macros require that `PartialEq` and `Debug` traits to be implemented on the data types they are comparing. For structs and enums this is as simple as adding the `#[derive(PartialEq, Debug)]` annotation to the definition.
- All three macros also provide for the addition of custom failure messages.
```rust
#[test]
fn greeting_contains_name() {
	let result = greeting("Carol");
	assert!(
		result.contains("Carol"), // Check if Carol was in the greeting.
		"Greeting did not contain name. Greeting was: {}", result // fail message
	)
}
```

### Checking for Panics
- Rust provides the `should_panic` attribute to check if the code inside the function panics. The test is considered a success if the code panics and a failure otherwise.
```rust
#[test]
#[should_panic]
fn foobar {} // Code is expected to panic.
```
- Tests that use `should_panic` can be imprecise—tests would pass even if the test case panics for an unexpected reason. `should_panic` can be mode precise by adding the optional `expected` parameter to the attribute. With `expected` the test is considered a success if it panics with a panic message containing the string specified by `expected`.
```rust
#[test]
#[should_panic(expected = "less than or equal to 100")]
fn foobar {} // Code is expected to panic.
```