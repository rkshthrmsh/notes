# Advanced Features
#rust 

does this update?
## [[Unsafe Rust]]

## [[Advanced Traits]]

## Advanced Types
### Using the Newtype Pattern for Type Safety and Abstraction
- The [[Advanced Traits#Implementing External Traits on External Types|newtype]] pattern is a lightweight way to achieve [[OOP Features of Rust#Characteristics of Object-Oriented Languages#Encapsulation|encapsulation]].

### Creating Type Synonyms with Type Aliases
- Rust provides the ability to declare a _type alias_ to give an existing type another name. For this we use the `type` keyword.
```rust
type Kilometers = i32;
let x: i32 = 5;
let y: Kilometers = 5;
println!("{}", x + y);
```
- This method merely renames the type and provides no type checking benefits that we got from the [[Advanced Traits#Implementing External Traits on External Types|newtype]] pattern.
- The main sue case is to reduce repetition. For this reason they are also commonly used with the `Result<T, E>` type. For this reason, `std::io` has a type alias declaration:
```rust
type Result<T> = std::result::Result<T, std::io::Error>;
```

### The Never Type that Never Returns
- Rust has a special type named `!` that’s known in type theory lingo as the _empty type_ because it has no values. 
- Rust prefers to call it the _never type_ because it stands in the place of the return type when a function will never return.
```rust
fn bar() -> ! {
	// body
}
```
- Functions that never return are called _diverging functions_. `bar` above is an example.
- The never type is useful in situations where a function has to deal with `panic!` or `continue` statements. The Rust compiler does not permit a function to have different return types. For example, a function cannot return both an integer and a string based on a conditional statement. However, a situation like this is exactly what occurs in the following common-place code.
```rust
let guess: u32 = match guess.trim().parse() {
	Ok(num) => num,
	Err(_) => continue, // return type of continue is not same as num.
}
```
- In the above example, `continue` expression has the type `!`. That is, when Rust computes the type of `guess`, it looks at both match arms, the former with a value of `u32` and the latter with a `!` value. Because `!` can never have a value, Rust decides that the type of `guess` is `u32`. This is also true for the `panic!` macro.

- Finally, a never ending `loop` also has the `!` type.

### Dynamically Sized Types and the `Sized` Trait
- Dynamically Sized Types or DSTs or unsized types let us write code using values whose size we can know only at runtime.
- An example of a DST in Rust is `str`. We can’t know how long the string is until runtime, meaning we can’t create a variable of type `str`, nor can we take an argument of type `str`.
- **Rust needs to know how much memory to allocate for any value of a particular type, and all values of a type must use the same amount of memory**. This is why it is not possible to create a variable holding a dynamically sized type.
- Therefore, Rust uses the `&str` type for string slices. A `&str` is _two_ values: the address of the `str` and its length. As such, we can know the size of a `&str` value at compile time: it’s twice the length of a `usize`
- **The golden rule of dynamically sized types is that we must always put values of dynamically sized types behind a pointer of some kind.**

- Every trait is a dynamically sized type we can refer to by using the name of the trait.
- To work with DSTs, Rust provides the `Sized` trait to determine whether or not a type’s size is known at compile time. This trait is automatically implemented for everything whose size is known at compile time. 
- In addition, Rust implicitly adds a bound on `Sized` to every generic function. That is, a generic function definition like this:
```rust
fn generic<T>(t: T) { }
// is equivalent to
fn generic<T: Sized>(t: T) { }
```

- By default, generic functions will work only on types that have a known size at compile time. However, you can use the following special syntax to relax this restriction:
```rust
fn generic<T: ?Sized>(t: &T) { } // Only available for Sized trait.
```
## [[Advanced Function and Closures]]
