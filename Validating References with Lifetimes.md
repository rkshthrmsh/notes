## Validating References with Lifetimes
- Every reference in Rust has a lifetime, which is the scope for which the reference is valid.
- The main aim of lifetimes is to prevent dangling references, which cause the program to reference data other that the data it is intended to reference. For instance, a reference to a value created within a function is a dangling reference.
- Lifetime annotations describe the relationships between the lifetimes of multiple references to each other. However, they do not affect the lifetimes of any references. The syntax of lifetime annotations looks like this:
```rust
&'a i32 // a reference with explicit lifetime
&'a mut i32 // a mutable reference with explicit lifetime
```
- Example use of lifetime annotations and their meaning:
```rust
fn longest<'a>(x: &'a str, y: &'a str) -> &'a str {}
```
This function signature tells the Rust compiler that `longest` takes at least two parameters, both of which are strings that live at least as long as lifetime `'a`. `a` here is arbitrary and just an indication. When `x` and `y` arguments are passed to the function, `'a` (and consequently the return from the function) will get the concrete lifetime that is equal to the smaller of the lifetimes of `x` and `y`.
 
- Structs that hold references as one of their fields would also need to be lifetime annotated.
```rust
struct ImportantExcerpt<'a> {
	part: &'a str,
}
```

- Because of the common occurrence of certain lifetime patterns, Rust's compiler includes these preprogrammed patterns to make function signatures more concise. The patterns programmed into Rust in this way are called the *lifetime elision rules*.
- The compiler uses three rules to figure out the lifetimes of references when there aren't explicit annotations. 
	- The first rule is that the compiler assigns a different lifetime parameter to each lifetime in each input
		- The function `fn foo(x: &i32)` would get one lifetime parameter and become `fn foo<'a>(x: &'a i32)`.
		- The function `fn foo(x: &i32, y: &i32)` would get two lifetime parameters and become `fn foo<'a, 'b>(x: &'a i32, y: &'b i32)`.
		- The function `fn foo(x: &ImportantExcerpt)` would get two lifetime parameters and become `fn foo<'a, 'b>(x: &'a ImportantExcerpt<'b>)`.
	- The second rule is that if there is a single input lifetime parameter, that lifetime is assigned to all the output lifetime parameters.
		- `fn foo<'a(x: &'a i32) -> &'a i32`
	- The third rule is that if there are multiple input lifetime parameters, but one of them is `&self` or `&mut self`, the lifetime of `self` is assigned to all output lifetime parameters.

- In method signatures inside the `impl` block, references might be tied to the lifetime of references in the struct's fields, or they may be independent.
```rust
impl<'a> ImportantExcerpt<'b> { // lifetimes can be independent
	fn level(&self) -> &str { } // lifetime annotation is implicit
}
```

- `'static` is a special lifetime which denotes that the reference can live for the entire duration of the program. All string literals implicitly have the `'static` lifetime.