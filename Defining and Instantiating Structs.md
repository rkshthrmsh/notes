## Defining and Instantiating Structs
- Structs are similar to tuples because they both hold multiple related values of different types. 
- However, unlike tuples, in structs, each value will have a label / name.
- Because of the labels, there is no strict ordering in structs.
- An example of defining a struct is:
```rust
struct User {
	active: bool,
	username: String,
	email: String,
	sign_in_count: u64,
}
```
- To use a struct after defining it, we can use the following construct.
```rust
fn main() {
	let user1 = User {
		email: String::from("someone@example.com")},
		username: String::from("someusername123"),
		active: true,
		sign_in_count = 1,
	};
}
```
- A specific value from the struct is obtained by using the dot notation: `user1.email`.
- In order to modify any of the struct's fields, the entire instance of the struct must be mutable. In Rust, individual fields cannot be marked as mutable.
- When structs are used within functions with the same parameter names as the struct fields, Rust provides a shorthand for initializing structs. For example:
```rust
fn build_user(email: String, username: String) -> User {
	User {
		email,
		username,
		active: true,
		sign_in_count: 1
	}
}
```
- Often, it is useful to create an instance of a struct by taking values from another instance of the struct. This can be done as follows:
```rust
let user2 = User {
	email: String::from("another@example.com"),
	..user1 // This must come last to specify take all remaining field of user1.
};
```
- **Note**: This struct update syntax works like an assignment. Therefore, if the fields being used from the other struct are non-`Copy` types, then the data moves away from `user1` in the example above. 
- Two different struct definitions with the same field names and types cannot be used interchangeably.

### Tuple Structs
- Tuple structs are structs that look similar to tuples.
- They do not have labels associated with fields, but only types.
- They are useful when it is necessary to give a particular tuple structure a name. For example:
```rust
struct Color(i32, i32, i32);
struct Point(i32, i32, i32);

fn main() {
	let black = Color(0, 0, 0);
	let origin = Point(0, 0, 0); // black != origing although contents are same.
}
```

### Unit-Like Structs
- Structs can also be defined to be unit-like, i.e., without any fields. These are useful for implementing traits without storing any data.
```rust
struct AlwaysEqual;

fn main() {
	let subject = AlwaysEqual;
}
```

- When it comes to permissions, Rust tracks fine-grained ownership of each struct's members. This is similar to permissions of the tuple type.
![[Pasted image 20230609190055.png]]

- Rust provides many in-built attributes for adding useful behavior to custom types such a structs. One such attribute is useful for printing structs, which cannot normally be printed using the `println!` statement.
```rust
#[derive(Debug)]
struct Point {
	x: i32,
	y: i32,
}
// later
let p = Point { x: 1, y: 2 };
println!("{:#?}", p); // :#? for pretty print and :? for print
```