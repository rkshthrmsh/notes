## Method Syntax
- Methods are similar to functions, but are defined in the context of a struct, enum, or a trait object.
- The first parameter of a method is always `self` which will be of type `Self`.
```rust
struct Rectangle {
	width: u32,
	height: u32,
}

imple Rectangle {
	fn area(&self) -> u32 { // &self is shorthand for self: &Self
		self.width * self.height
	}
}

fn main() {
	let rect1 = Rectangle {
		width: 30,
		height: 50,
	};

	println!("Area of rectangle is {}", rect1.area());
}
```
- Methods can take ownership of `self`, borrow it immutable, or mutably, just like any other parameter.

### Associated Functions
- Associated functions are functions within an `impl` block that don't have `self` as their first parameter. 
- Associated functions are often used as constructors for the struct they are associated with. For example, consider the following associated function of `Rectangle` which is used to construct a square.
```rust
impl Rectangle {
	fn square(size: u32) -> Self { // Self here refers to Rectangle
		width: size,
		height: size,
	}
}

// Example usage
let sq = Rectangle::square(3);
```

- Method calls are syntactic sugar for function calls. Rust adds as many reference (&) and dereference (\*) operators as necessary to make the types of the method match up for the self parameter.
- Rust protects structs from unsafe behavior, such as double-free, by default. This is especially useful when the struct fields contain non-`Copy` type data. In situations where the user is sure of safe behavior, the `#[derive(Copy)]` trait can be added before the struct definition to indicate that the struct is always expected to have `Copy` data type only.
- When an error like "cannot move out of `*self`" occurs, it is usually because of attempts to call a `self` method on a reference like `&self` or `&mut self`.