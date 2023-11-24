## Defining an Enum
- Enum is an appropriate data structure for values which can only be one of its possible variants at a given time. For example:
```rust
enum IpAddrKind { // IP addresses can only be v4 or v6.
	v4,
	v6, // v4 and v6 are the variants.
}

// Create instances
let four = IpAddrKind::v4;
let six = IpAddrKind::v6; // both four an six are of IprAddrKind type.
```
- Enum variants are considered as being the same type. This makes it easy to implement functions that handle all variants of an enum.
- An additional advantage of enums is that they can be used to represent data very concisely. Consider the example of adding an address to the enum defined above. One approach would be to use structs:
```rust
struct IpAddr { // struct with enum inside.
    kind: IpAddrKind,
    address: String,
}

let home = IpAddr {
    kind: IpAddrKind::V4,
    address: String::from("127.0.0.1"),
};

let loopback = IpAddr {
    kind: IpAddrKind::V6,
    address: String::from("::1"),
};
```
- However, this can be achieved more concisely by **associating data directly with the enum's variants**. Through this mechanism, the name of each enum variant becomes a function that constructs an instance of the enum: `IpAddr::v4()` is a function call that takes a `String` argument and returns an instance of the `IpAddr` type.
```rust
enum IpAddr {
	v4(String),
	v6(String),
}

let home = IpAddr::v4(String::from("127.0.0.1"));
let loopback = IpAddr::v6(String::from("::1"));
```
- Moreover, **each variant can have different types and amounts of data**. There is no limitation to the type of associated data—it can be strings, numeric types, structs, and even enums.
```rust
enum IpAddr{
	v4(u8, u8, u8, u8),
	v6(String),
}

enum Message {
	Quit,
	Move { x:i32, y: i32 },
	Write(String),
	ChangeColor(i32, i32, i32),
}
```
- Even though the last example above can be represented using four different structs, the enum definition has the advantage of being able to define a single structure to handle all the variants.
- Just like structs, methods can be defined on enums.

### The `Option` Enum and its Advantages Over Null
- Rust does not have the null feature that many other languages implement.
- The implementations of null values in other programming languages have a problem, which is that any attempt to use a null value as a non-null value leads to errors.
- Rust does not have nulls, but it does have an enum that can encode the concept of a value being present or not.
```rust
enum Option<T> {
	None,
	Some(T),
}
```
- By implementing null as a generic type, Rust ensures that `Option<T>` and `T` are different types. Therefore, the compiler will prevent the usage of `Option<T>` as if it were definitely a valid value.
- In order to use the value within `Option<T>`, it needs to be converted into a `T`. This eliminates the risk of incorrectly assuming a not-null value. Everywhere the value has a non `Option<T>` type, it is safe to assume that the value isn't null.