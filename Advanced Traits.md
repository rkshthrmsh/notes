## Advanced Traits
### Specifying Placeholder Types in Trait Definitions with Associated Types
- _Associated types_ connect a type placeholder with a trait such that the trait method definitions can use these placeholder types in their signatures.
- The implementor of a trait will specify the concrete type to be used instead of the placeholder type for the particular implementation. 
- That way, we can define a trait that uses some types without needing to know exactly what those types are until the trait is implemented.
```rust
pub trait Iterator {
	type Item;

	fn next(&mut Self) -> Option<Self::Item>;
}
```
- Although associated types look similar to generic types, they have some differences: With generics, types need to be annotated with each implementation. This implies that the same trait can be implemented multiple times, with the generic type being replaced with a concrete type each time. However, with associated types, we can only have one concrete type replace the associated type since there can only be one `impl` block with a unique method name. 
- For the above reason, associated type also becomes a part of the trait's contract.

### Default Generic Type Parameters and Operator Overloading
- When we use generic type parameters, we can specify a default concrete type for the generic type.
- This is done when declaring a generic type with the `<PlaceholderType=ConcreteType` syntax.
- This technique is useful in operator overloading, which is used to customize the behavior of existing operators such as `+`. Operator overloading is done by implementing the traits associated with the operator. For example, the `Add` trait is defined as in the following example.
```rust
trait Add<Rhs=Self> {
	type Output;
	fn add(self, rhs: Rhs) -> Self::Output;
}
```
- Since add provides a default type, it can be overloaded. The following example shows operator overloading for adding millimeters and meters together.
```rust
use std::ops::Add;

struct Millimeters(u32);
struct Meters(u32);

impl Add<Meters> for Millimeters {
	type Output = Millimeters;
	fn add(self: other: Meters) -> Millimeters {
		Millimeters(self.0 + (other.0 * 1000))
	}
}
```
- Default type parameters are used in two ways:
	- To extend a type without breaking existing code.
	- To allow customization in specific cases most users won't need.

### Fully Qualified Syntax for Disambiguation
- Sometimes, certain traits implemented on a data type may have the same names for the associated functions. In these situations disambiguation is necessary to ensure that the Rust compiler understands exactly which associated function is being called. 
- The disambiguation needs to deal with two scenarios: when the associated function is a method, i.e., when it takes a self parameters, and when it is not a method.
- Consider the following example with different trait methods having the same name.
```rust
trait Pilot {
	fn fly(&self);
}

struct Human;

impl Pilot for Human {
	fn fly(&self) {
		println!("This is your captain speaking");
	}
}

impl Human {
	fn fly(&self) {
		println!("*waving arms furiously*");
	}
}

fn main() {
	let person = Human;
	Pilot::fly(&person); // disambiguate fly for Pilot trait
	person.fly(); // equivalent to Human::fly(&person)
}
```

- The format of disambiguation used above will not work when the associated function is not a method, i.e., when it does not take a self parameter. In this case, it is impossible for Rust to tell which function of the many different data structures implementing the trait is being called. A fully qualified syntax is needed for discerning.
```rust
trait Animal {
	fn baby_name() -> String;
}

struct Dog;

impl Dog {
	fn baby_name() -> String {
		String::from("Spot")
	}
}

impl Animal for Dog {
	fn baby_name() -> String {
		String::from("puppy")
	}
}

fn main() {
	println!("{}", Dog::baby_name());
	println!("{}", <Dog as Animal>::baby_name()); // Animal::baby_name() will not work
}
```

### Using Supertraits
- A supertrait is a trait that uses the functionality of another trait in its own definition.
- The following is an example of a supertrait `OutlinePrint` that formats the output of any data type implementing the `Display` trait. The format for specifying that the trait depends on another trait is
```rust
use std::fmt;

trait OutlinePrint: fmt::Display { // syntax
	fn outline_print(&self) {
		// default implementation
	}
}
```

### Implementing External Traits on External Types
- The orphan rule that states we’re only allowed to implement a trait on a type if either the trait or the type are local to our crate. 
- It’s possible to get around this restriction using the _newtype pattern_, which involves creating a new type in a tuple struct.
- As an example, let’s say we want to implement `Display` on `Vec<T>`, which the orphan rule prevents us from doing directly because the `Display` trait and the `Vec<T>` type are defined outside our crate. We can make a `Wrapper` struct that holds an instance of `Vec<T>`; then we can implement `Display` on `Wrapper` and use the `Vec<T>` value.

```rust
use std::fmt;

struct Wrapper(Vec<String>); // work-around

impl fmt::Display for Wrapper {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        write!(f, "[{}]", self.0.join(", "))
    }
}

fn main() {
    let w = Wrapper(vec![String::from("hello"), String::from("world")]);
    println!("w = {}", w);
}
```
- The downside is `Wrapper` is a new type, so it doesn’t have the methods of the value it’s holding.
- If we wanted the new type to have every method the inner type has, implementing the [[Smart Pointers#Treating Smart Pointers Like Regular References with the `Deref` Trait|Deref]]` trait.