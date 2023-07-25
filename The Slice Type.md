## The Slice Type
- Slices are references that allow referencing a contiguous sequence of elements in a collection rather than the whole collection.
- Without slices operations that require keeping track of indexes within data types such as `String` and arrays would be a tedious and unsafe task. 
- Slices, also called "fat" pointers because of a larger size compared to normal pointers, make such operations manageable. The larger size is because slices need to store slice length information.
![[Pasted image 20230609150219.png]]
- Since slices are references, they also change permissions on referenced data, much like normal pointers.
- Slices can be represented using `..` syntax
```rust
let s = String::from("hello world");
let len = s.len();
let s2 = &s[0..len];
let hello = &s[..5]; // Starting 0 can be dropped.
let world = &s[6..]; // Ending index can also be dropped.
let s3 = &s[..]; // starting and ending indices can be dropped.
```
- A `String` type slice has the type `&str`
```rust
// Return first word in a string.
fn first_word(s: &String) -> &str {
	let bytes = s.as_bytes();
	for (i, &item) in bytes().iter().enumerate() {
		if item == b' ' {
			return &s[..i]
		}
	}
	&s[..]
}
```
- String literals are slices
`let s = "Hello, world!"; // This is a string slice: &str`
- In most situation it is useful to rewrite a parameter of type `&String` as a string slice `&str`. This is because a function that is written to handle the latter is always capable of handling the former.
```rust
// first_word parameter type can be rewritten.
fn first_word(s: &str) -> &str {
	// body
}
```
- Combined together, these aspects of the string slice enables many different possibilities:
```rust
fn main() {
    let my_string = String::from("hello world");

    // `first_word` works on slices of `String`s, whether partial or whole
    let word = first_word(&my_string[0..6]);
    let word = first_word(&my_string[..]);
    // `first_word` also works on references to `String`s, which are equivalent
    // to whole slices of `String`s
    let word = first_word(&my_string);

    let my_string_literal = "hello world";

    // `first_word` works on slices of string literals, whether partial or whole
    let word = first_word(&my_string_literal[0..6]);
    let word = first_word(&my_string_literal[..]);

    // Because string literals *are* string slices already,
    // this works too, without the slice syntax!
    let word = first_word(my_string_literal);
}
```

- Similar to string slices, arrays can also be sliced. The array slice is represented by the type `&[i32]`.