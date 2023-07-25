## `Rc<T>, the Reference Counted Smart Pointer`
- Reference counting provides a way for multiple ownership.
- `Rc<T>` keeps track of of the number of references to a value to determine whether or the value is still in use.
- `Rc<T>` is only for use in single-threaded scenarios.
- Consider the following scenario where a cons list `a` is being shared, and extended, by cons lists `b` and `c`. Trying to use the recursive data type created using a `Box<T>` will not work since the creation of `b` moves ownership of `a`. This is the type of scenario in which an `Rc<T>` would be useful.
![[Pasted image 20230615173321.png]]

```rust
enum List {
	Cons(i32, Rc<List>),
	Nil,
}

use crate::List::{Cons, Nil};
use std:rc:Rc;

fn main() {
	let a = Rc::new(Cons(5, Rc::new(Cons(10, Rc::new(Nil)))));
	let b = Cons(3, Rc::clone(&a));
	let c = Cons(4, Rc::clone(&a));
}
```
- Every time we call `Rc::clone`, the reference count to the data within the `Rc<List>` will increase, and the data won’t be cleaned up unless there are zero references to it.
- The implementation of `Rc::clone` doesn’t make a deep copy of all the data like most types’ implementations of `clone` do. The call to `Rc::clone` only increments the reference count, which doesn’t take much time. Deep copies of data can take a lot of time. By using `Rc::clone` for reference counting, we can visually distinguish between the deep-copy kinds of clones and the kinds of clones that increase the reference count.
- The `Rc::strong_count` function can be used to print the number of reference counts to the data.
- The implementation of the `Drop` trait decrements the reference count automatically.