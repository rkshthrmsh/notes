## Using `Box<T>` to Point to Data on the Heap
- Boxes allow storing data on the heap rather than the stack.
- Boxes don't have performance overhead. They are most often used for:
	- Types whose size can't be know at compile time.
	- Ownership of large amounts of data that needs to be moved instead of copied.
	- Owning a value of a type that implements a particular trait.
- Creating a new box
```rust
fn main() {
	let b = Box::new(5);
	println!("{}", b);
}
```

### Enabling Recursive Types with Boxes
- Recursive types can have another value of the same type as a part of itself. 
- Since the Rust compiler has no way of understanding how much space a recursive type takes up, these types can become an issue.
- Using boxes can help break the recursive type definition into 
- An example of a recursive type is a cons list, which is a data structure containing a nested pair. The first element in the pair is the value of the current item and the second element is the next value. 
`(1, (2, (3, Nil)))`
- A basic implementation in Rust would look like the following code. However, the compiler will throw an error since it is unable to assess the amount of space required for this data type.
```rust
enum List {
	Cons(i32, List),
	Nil,
}
```
![[Pasted image 20230615161555.png]]

- The workaround here would be store the value indirectly by storing a pointer to the value instead.
```rust
enum List {
	Cons(i32, Box<List>),
	Nil,
}
```
![[Pasted image 20230615161921.png]]