## Advanced Function and Closures
### Function Pointers
- Similar to how [[Closures|closures]] can be passed to functions, functions can also be passed to functions.
- Functions coerce to the type `fn` (not the same as `Fn`). The `fn` type is called a *function pointer*.
```rust
fn add_one(x: i32) -> i32 {
	x + 1
}

fn do_twice(f: fn(i32) -> i32, arg: i32) -> i32 {
	f(arg) + f(arg)
}

fn main() {
	let answer = do_twice(add_one, 5);
}
```
- Unlike closures, `fn` is a type rather than a trait. Therefore, `fn` is used directly as the parameter type rather than declaring a generic type parameter with one of the `Fn` (closure) traits as the trait bound.
- Function pointers implement all three of the closure traits (`Fn`, `FnMut`, and `FnOnce`), which means `fn` can always be passed as an argument for a function that expects a closure. An example of such a function is:
```rust
let list = vec![1, 2, 3];

// covert elements to string using closure
let list_strings = list.iter().map(|i| i.to_string()).collect();

// using function pointers instead
let list_strings = list.iter().map(ToString::to_string).collect();
```
- This can be a useful mechanism for generating enum variants. Recall that [[Defining an Enum|enum variants are themselves initializer functions]].
```rust
enum Status {
	Value(u32),
	Stop,
}
let list_statuses = (0u32..20).map(Status::Value).collect();
```

### Returning Closures
- Since closures are represented by traits, they cannot be returned directly.
- We have already seen one technique for [[Closures|returning closures]]. This is recalled in the code snippet below. Depending on the functionality implemented by the closure the trait can be `Fn`, `FnMut`, or `FnOnce`.
```rust
fn return_closure() -> impl Fn(i32) -> i32 { // can be FnMut or FnOnce
	|x| x + 1
}
```
- In situations where Rust is unable to understand how much size is required to store the closure, a [[Smart Pointers#Box Pointer Using `Box<T>` to Point to Data on the Heap|box pointer]] with a `dyn` keyword (similar to [[Trait Objects]]) is necessary.
```rust
fn returns_closure() -> Box<dyn Fn(i32) -> i32> {
	Box::new(|x| x + 1)
}
```