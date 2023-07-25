## Control Flow
### `if` Expressions
```rust
let num = 1;

if num > 0 {
	// statements
} else if num == 1 {
	// statements
} else {
	// statements
}

let condition = true;
let num = if condition { 5 } else { 6 };
```

### Repetition with Loops
- Rust has three kinds of loops: `loop`, `while`, and `for`.

#### Repeating Code with `loop`
- Executes a block of code over and over again forever until explicitly exited.
- `break` and `continue` can be used to control the execution of the `loop`.
- `break` can also be used to return a value from the loop while simultaneously exiting the loop. 
- Loops can also be labeled which is helpful in disambiguation where nested loops with `break` or `continue` statements are used.
```rust
fn main() {
	let mut condition = 0;
	let result = loop {
		counter += 1;
		if counter == 10 {
			break counter * 2; // Return value. Technicaly an expression.
		}
	};

	'counting_up: loop { // loop label
		// statements
		break 'counting_up
	}
}
```

#### Conditional Loops with `while`
```rust
let num = 2;
while num != 0 {
	// statements
}
```

#### Looping through a Collection with `for`
- Most commonly used loop construct in Rust due to the conciseness and safety.
```rust
let a = [1, 2, 3, 4];
for element in a {
	// statemnts
}

for num in (1..4) { // specify number range
	// statements
}
```