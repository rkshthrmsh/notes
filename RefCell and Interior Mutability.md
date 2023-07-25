## `RefCell<T>` and the Interior Mutability Pattern
- Interior mutability is a design pattern in Rust that allows you to mutate data even when there are immutable references to that data; normally, this action is disallowed by the borrowing rules.
- Mutating data uses `unsafe` code to bend the Rust compiler's usual borrow checker rules.
- Unlike `Rc<T>`, the `RefCell<T>` type represents single ownership over the data it holds.
- The borrowing rules state that 
	- At any given time, you can have _either_ (but not both) one mutable reference or any number of immutable references.
	- References must always be valid.
- With references and `Box<T>`, the borrowing rules’ invariants are enforced at compile time. With `RefCell<T>`, these invariants are enforced _at runtime_. 
- With references, if you break these rules, you’ll get a compiler error. With `RefCell<T>`, if you break these rules, your program will panic and exit.
- `RefCell<T>` is useful when the user is certain that their code follows the borrowing rules but the compiler is unable to understand and guarantee that.
- With `RefCell<T>`, we use the `borrow` and `borrow_mut` methods, which are part of the safe API that belongs to `RefCell<T>`. The `borrow` method returns the smart pointer type `Ref<T>`, and `borrow_mut` returns the smart pointer type `RefMut<T>`.
- The `RefCell<T>` keeps track of how many `Ref<T>` and `RefMut<T>` smart pointers are currently active. Just like the compile-time borrowing rules, `RefCell<T>` lets us have many immutable borrows or one mutable borrow at any point in time.

### Having Multiple Owners of Mutable Data by Combining `Rc<T>` and `RefCell<T>`
- A common way to use `RefCell<T>` is in combination with `Rc<T>`.This allows a value to have multiple owners and can be mutated.
- Going back to the cons list example from the [[Reference Counting Pointer|`Rc<T>`]] section.
```rust
#[derive(Debug)]
enum List {
	Cons(Rc<RefCell<i32>>, Rc<List>),
	Nil,
}

use crate::List::{Cons, Nil};
use std::cell::RefCell;
use std::rc:Rc;

fn main() {
	let value = Rc::new(RefCell::new(5));
	let a = Rc::new(Cons(Rc::clone(&value), Rc::new(Nil)));
	let b = Cons(Rc::new(RefCell::new(3)), Rc::clone(&a));
	let c = Cons(Rc::new(RefCell::new(4)), Rc::clone(&a));

	*value.borrow_mut() += 10;

	println!("a after = {:?}", a);
	println!("b after = {:?}", b);
	println!("c after = {:?}", c);
}
```