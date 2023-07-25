## Traits: Defining Shared Behavior
- Trait bounds can specify that a generic type can be any type that has a certain behavior.
- Trait definitions are a way to group method signatures together to define a set of behaviors necessary to accomplish some purpose.
- A trait can have multiple methods in its body: the method signatures are listed one per line and each line ends in a semicolon.
- Each type implementing a trait must provide its own custom behavior for the body of the methods listed in the trait.
- The following example makes this clear.
```rust 
pub trait Summary {
	fn summarize(&self) -> String;
}
```
-  Types implementing `Summary` trait must provide custom implementations of `summarize` method.
``` rust
pub struct NewsArticle {
	pub headline: String,
	pub location: String, 
	pub author: String,
	pub content: String,
}

impl Summary for NewsArticle {
	fn summarize(&self) -> String {
		format!("{}, by {} ({})", self.headline, self.author, self.location)
	}
}

pub struct Tweet { 
	pub username: String,
	pub content: String,
	pub reply: bool,
	pub retweet: bool,
}

impl Summary for Tweet {
	fn summarize(&self) -> String {
		format!("{}: {}", self.username, self.content)
	}
}
```
- After trait implementation, users can call the trait methods just like other methods. The only difference is that the trait itself must be brought into scope.
```rust
use aggregator::{Summary, Tweet}; // aggregator is the module here.

// -- snip --
let tweet = Tweet { 
	// -- snip --
};
println!("1 new tweet: {}", tweet.summarize());
```
- One restriction of note is that a trait can only be implemented if either the trait or the type is local to the crate.

- Default methods can also be implemented during the trait definition to provide a useful baseline for the types that may use this trait. Such default traits can even call yet-to-be-implemented methods in the trait.
```rust
pub trait Summary {
	fn summarize_author(&self) -> String;

	// default trait method
	fn summarize(&self) -> String {
		String::from("(Read more...)", self.summarize_author())
	}
}
```


### Traits as Parameters
- Having defined traits, they can then be used as parameters to functions. All types that implement the trait would be a valid input to such a function.
```rust
// Option1: impl Trait syntax:
pub fn notify(item: &impl Summary) { // annotation for types implementing Summary trait.
	println!("Breaking news! {}", item.summarize);
}

// Option 2: Trait Bound syntax:
pub fn notify<T: Summary>(item: &T) {
	// same body as above
}
```

- This can be taken a step ahead to define multiple trait bounds. In this each parameter with multiple trait bounds needs to implement all the traits.
```rust
// impl Trait syntax
pub fn notify(item: &(impl Summary + Display)) {}

// Trait Bound syntax
pub fn notify<T: Summary + Display>(item: &T) {}
```

- A `where` clause syntax can be used to make method definitions clearer when dealing with multiple trait bounds.
```rust
// where clause syntax
pub fn notify<T, U>(item1: &T, item2: &U)
where
	T: Summary + Display,
	U: Clone + Debug,
{
	// method body
}
```

### Returning Types that Implement Traits
```rust
fn returns_summarizable() -> impl Summary { // returns a type implementing Summary
}
```
- However, `impl` Trait as a return type will only work if the function returns a single type. For example, the following code will not work because two different types are being returned.
```rust
fn returns_summarizable(switch: bool) -> impl Summary {
	if switch {
		// return Tweet
	} else {
		// return NewsArticle
	}
}
```
- The work around here is to use [[Trait Objects#Using Trait Objects That Allow for Values of Different Types|trait objects]] instead.

### Using Trait Bounds to Conditionally Implement Methods
- Using traits with generic types enables the implementation of methods conditionally based on the traits implemented by different types.
```rust
use std::fmt::Display;

struct Pair<T> {
	x: T,
	y: T,
}

impl<T> Pair<T> {
	fn new(x: T, y: T) -> Self {
		Self { x, y }
	}
}

impl<T: Display + PartialOrd> Pair<T> {
	fn cmp_display(&self) {
		if self.x >= self.y {
			println!("The largest member is: {}", self.x);
		} else {
			println!("The largest member is: {}", self.x);
		}
}
}
```