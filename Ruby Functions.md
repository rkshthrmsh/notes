## Functions
- *Snake case* is the conventional style for function names in Rust.
- Type annotation of parameters is mandatory.
- The function body includes a set of statements followed by an expression.
```rust
fn example_function(x: i32) { // type annotation of parameters is mandatory.
	// function body
}
```

#### Statements and Expressions
- Statements are instructions that perform an action but do not return a value. For example, creating a variable and assigning a value to it with `let` is a statement. Function definitions are statements as well.
- Expressions evaluate to a value. Therefore, they can be a part of a statement. Examples of expressions include calling functions and macros. Even new scopes created with curly brackets can be an expression. **Expressions do not include ending semicolons**.
```rust
fn main() {
	let y = {
		let x = 3;
		x + 1 // note the lack of ending semicolon.
	};
}
```

#### Functions with Return Values
- Return value types myst be annotated in the function definition / header.
- Functions can return early using the `return` keyword.
- However, most functions implicitly return the last expression.
```rust
fn plus_one(x: i32) -> i32 { // type annotation for the return value.
	x + 1 // note that this is an expression (no ending semicolon)
}
```