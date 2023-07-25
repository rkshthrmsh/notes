# Enums and Pattern Matching
#rust 

## [[Defining an Enum]]

## The `match` Control Flow Construct
- `match` allows comparison of code against a series of patterns and executes code based on which pattern matches.
- The power of `match` comes from the expressiveness of the patterns and that the compiler ensures that all possible cases are handled.
- Another useful feature of match arms is that they can bind to the parts of the values that match the pattern. This can be used to extract values out of arms with enum variants.
```rust
enum UsState {
	Alabama,
	Alaska,
	// and so on
}
enum Coin {
	Penny,
	Nickel,
	Dime,
	Quarter(UsState),
}

fn value_in_cents(coin: Coin) -> u8 {
	match coin {
		Coin::Penny => {
			println!("Lucky penny!");
			1
		}
		Coin:: Nickel => 5,
		Coin::Dime => 10,
		Coin::Quarter(state) => {
			println!("State {:?}", state);
			25,
		}
	}
}
```
- In a match statement, `_` represents a catch-all and do not bind and `()` represents do nothing: `_ => ()`

## Concise Control Flow with `if let`
- `if let` is a combination of `if` and `let` to deal with values that match just one pattern in a more concise way. For example:
```rust
let config_max = Some(3u8);
match config_max {
	Some(max) => println!("{max}"),
	_ => () // do nothing
}

// equivalent to
if let Some(max) = config_max {
	println!("{max}");
}
```
- `if let` takes a pattern and an expression separated by an equal sign.
- This construct requires lesser typing, indentation, and boilerplate code. However, there is no exhaustive checking by the compiler.
- `if let` can also have an optional `else` arm
```rust
let mut count = 0;
match coin {
	Coin::Quarter(state) => println!("{:?}", state),
	_ => count += 1,
}

if let Coin::Quarter(state) = coin {
	println!("{:?}", state);
} else {
 count += 1;
}
```