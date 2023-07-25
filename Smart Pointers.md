# Smart Pointers
#rust 

- A _pointer_ is a general concept for a variable that contains an address in memory. _Smart pointers_, on the other hand, are data structures that act like a pointer but also have additional metadata and capabilities.
- Smart pointers are usually implemented using structs. Unlike an ordinary struct, smart pointers implement the `Deref` and `Drop` traits. 
	- The `Deref` trait allows an instance of the smart pointer struct to behave like a reference so you can write your code to work with either references or smart pointers. 
	- The `Drop` trait allows you to customize the code that’s run when an instance of the smart pointer goes out of scope.
- The most common smart pointers in the standard library include:
	- `Box<T>` for allocating values on the heap.
	- `Rc<T>`, a reference counting type that enables multiple ownership.
	- `Ref<T>` and `RefMut<T>`, accessed through `RefCell<T>`, a type that enforces the borrowing rules at runtime instead of compile time.
 
- Here is a summary of the reasons to choose `Box<T>`, `Rc<T>`, or `RefCell<T>`:
	- `Rc<T>` enables multiple owners of the same data; `Box<T>` and `RefCell<T>` have single owners.
	- `Box<T>` allows immutable or mutable borrows checked at compile time; `Rc<T>` allows only immutable borrows checked at compile time; `RefCell<T>` allows immutable or mutable borrows checked at runtime.
	- Because `RefCell<T>` allows mutable borrows checked at runtime, you can mutate the value inside the `RefCell<T>` even when the `RefCell<T>` is immutable.

## [[Box Pointer|Using `Box<T>` to Point to Data on the Heap]]

## Treating Smart Pointers Like Regular References with the `Deref` Trait
- Implementing the `Deref` trait allows one to customize the behavior of the dereference pointer `*`. This enables smart pointers to work in ways similar to references.
- The `Deref` trait requires the implementation of one method, `deref`, that borrows `self` and returns a reference to the inner data.
- The following is an example of implementing a new smart pointer which can dereference its data.
```rust
use std::ops::Deref;

struct MyBox<T>(T);

impl<T> MyBox<T> {
	fn new(x: T) -> MyBox<T> {
		MyBox(x)
	}
}

impl<T> Deref for MyBox<T> {
	type Target = T; // associated type for the Deref trait

	fn deref(&self) -> &Self::Target {
		&self.0
	}
}
```

- Similarly, implementing the `DerefMut` trait will need to be implemented for mutable references.

### Deref Coercion
- Deref coercion converts a reference to a type that implements the `Deref` trait into a reference to another type. 
- This a convenience that Rust performs to functions and methods and works only on types that implement the `Deref` trait.
- Rust does deref coercion when it finds types and trait implementations in three cases:
	- From `&T` to `&U` when `T: Deref<Target=U>`
	- From `&mut T` to `&mut U` when `T: DerefMut<Target=U>`
	- From `&mut T` to `&U` when `T: Deref<Target=U>`. Rust will coerce a mutable reference to an immutable one, but the reverse is not possible. 

## Running Code on Cleanup Using the `Drop` Trait
- `Drop` lets you customize what happens when a value is about to go out of scope.
- This enables the user to specify a piece of code to be run whenever a value goes out of scope, and the compiler will insert this code automatically.
- The `Drop` trait requires the implementation of one method, `drop`, that takes a mutable reference to `self`.
- Variables are dropped in the reverse order of their creation.

- Sometimes it may be necessary to drop a value early. For this, Rust provides the `std::mem::drop` method which is already included in the prelude. Using this method ensures that the compiler understands and tracks the drop, therefore avoiding a double-free.

## [[Reference Counting Pointer|`Rc<T>, the Reference Counted Smart Pointer`]]

## [[RefCell and Interior Mutability|`RefCell<T>` and the Interior Mutability Pattern]]

## [[Reference Cycles can Leak Memory]]