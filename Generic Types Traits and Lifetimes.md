# Generic Types, Traits, and Lifetimes
#rust 

- Generics are abstract stand-ins for concrete types or other properties.
- Traits define functionality a particular type has and can share with other types.
- Lifetimes are a variety of generics that give the compiler information about how references relate to each other.

## [[Generic Data Types]]

## [[Traits- Defining Shared Behavior]]

## [[Validating References with Lifetimes]]

## Type Parameters, Trait Bounds, and Lifetimes Together
```rust
use std::fmt::Display;

fn longest_with_announcement<'a, T>(
	x: &'a str,
	y: &'a str,
	ann: T,
) -> &'a str
where
	T: Display,
{
	println!("Announcement: {}", ann);
	if x.len() > y.len() {
		x
	} else {
		y
	}
}
```