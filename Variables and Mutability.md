## Variables and Mutability
- By default, variables are immutable. This ensures that code safety and makes it easy to to implement concurrency.
- The variable assignment statements looks like what follows:
```rust
let x = 5; // immmutalble (default)
let mut x = 5; // mutable
```

### Constants
- Constants are values that are bound to a name and cannot be changed throughout program execution. They aren't just immutable by default—they are always immutable.
- Constant declaration must always be type annotated.
```rust
const x: u32 = 5;
```

### Shadowing
- A previously declared variable is said to be shadowed if a new `let` declaration uses the same name as the variable. As a consequence, the compiler only see the latest declaration of the variable.
- Shadowing is limited to the scope of declaration. For example:
```rust
fn main() {
	let x = 5;
	let x = x + 1; // x was shadowed by x + 1

	{
		let x = x * 2 // x was shadowed by x * 2 only in the inner scope*
		println!("{x}") // 12
	}

	println!("{x}") // 6 value restored to outer scope.
}
```
- Shadowing is different from `mut` variables. Shadowing variables can only be assigned to using the `let` keyword. This can be used to:
	- Do a few transformations on the variable and still have it be immutable.
	- Change the type of the variable and still reuse the same name. Mutable variables, on the other hand, cannot change their type.