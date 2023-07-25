## Generic Data Types
```rust
// In function definitions
fn largest<T>(list: &[T]) -> &T {
	let mut largest = &list[0];

	for item in list {
		if item > largest {
			largest = item;
		}
	}
	largest
}

// In struct definitions
struct Point<T> {
	x: T,
	y: T,
}

// In enum definitions
enum Result<T, E> {
	Ok(T),
	Err(E),
}

// In method definitions
impl<T> Point<T> { // associated with struct Point above
	fn x(&self) -> &T {
		&self.x
	}
}
// In method definitions with constraints
impl Point<f32> { // only applies to Points of type f32.
	fn distance_from_origin(&self) -> f32 {
		(self.x.powi(2) + self.y.powi(2)).sqrt()
	}
}
```

- Rust will reject simultaneous implementations of specific and generic methods of the same name because it will not know which  implementation to use.

- Generic types incur no performance cost because Rust compiles generic code into code that specifies the type in each instance. This is called monomorphization.