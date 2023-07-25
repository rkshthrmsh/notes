## Reference Cycles can Leak Memory
- A memory leak is said to happen when memory that has been created never gets cleaned up.
- An example of a memory leak is when there are reference cycles. Consider, again, the case of a cons list as shown in the code below. Here, the second element has the ability to modify the next value the cons list is pointing to. 
```rust
use crate::List::{Cons, Nil};
use std::cell::RefCell;
use std::rc::Rc;

#[derive(Debug)]
enum List {
	Cons(i32, RefCell<Rc<List>>),
	Nil,
}

impl List {
	fn tail(&self) -> Option<&RefCell<Rc<List>>> { // method to retreive 2nd elem
		match self {
			Cons(_, item) => Some(item),
			Nil => None,
		}
	}
}

fn main() {
	let a = Rc::new(Cons(5, RefCell::new(Rc::new(Nil))));
	let b = Rc::new(Cons(10, RefCell::new(Rc::clone(&a)))); // b points to a
	if let Some(link) = a.tail() { // link tail of a to b; creates ref cycle
		*link.borrow_mut() = Rc::clone(&b);
	}
}
```
![[Pasted image 20230616110449.png]]
- The reference cycle created above means that both `a` and `b` never have their `strong_count` down to `0` after program clean-up. As a consequence, they are never removed from memory, leading to a leak.
- If a program contains `RefCell<T>` values that contain `Rc<T>` values or similar nested combinations of types with interior mutability and reference counting, it must be ensured that no cycles are created; you can’t rely on Rust to catch them.

- Another solution for avoiding reference cycles is to reorganize the data structures such that some references express ownerships while others don't.
- The `Weak<T>` smart pointer is a good option in this case. Weak references don’t express an ownership relationship, and their count doesn’t affect when an `Rc<T>` instance is cleaned up. They won’t cause a reference cycle because any cycle involving some weak references will be broken once the strong reference count of values involved is 0.
- When you call `Rc::downgrade`, you get a smart pointer of type `Weak<T>`. Instead of increasing the `strong_count` in the `Rc<T>` instance by 1, calling `Rc::downgrade` increases the `weak_count` by 1. The `Rc<T>` type uses `weak_count` to keep track of how many `Weak<T>` references exist, similar to `strong_count`. The difference is the `weak_count` doesn’t need to be 0 for the `Rc<T>` instance to be cleaned up.
- Because the value that `Weak<T>` references might have been dropped, to do anything with the value that a `Weak<T>` is pointing to, you must make sure the value still exists. Do this by calling the `upgrade` method on a `Weak<T>` instance, which will return an `Option<Rc<T>>`. You’ll get a result of `Some` if the `Rc<T>` value has not been dropped yet and a result of `None` if the `Rc<T>` value has been dropped.
- An example of this is creating a tree structure where a node owns its children, and the ownership should be shareable so that the child nodes can be accessed directly. Further, the child node should also be aware of its parent. Ultimately, the parent node should own its children, and if a parent is dropped, so too its child nodes.
```rust
use std::cell::RefCell;
use std::rc::{Rc, Weak};

#[derive(Debug)]
struct Node {
	value: i32,
	parent: RefCell<Weak<Node>>, // weak reference to parent node
	children: RefCell<Vec<Rc<Node>>>, // ownership of child nodes
}

fn main() {
	let leaf = Rc::new(Node {
		value: 3,
		parent: RefCell::new(Weak::new()),
		children: RefCell::new(vec![]),
	});

	println!("leaf parent = {:?}", leaf.parent.borrow().upgrade());
	// upgrade needed to access parent from weak node.

	let branch = Rc::new(Node {
		value: 5,
		parent: RefCell::new(Weak::new()),
		children: RefCell::new(vec![Rc::clone(&leaf)]),
	});

	*leaf.parent.borrow_mut() = Rc::downgrade(&brand);
	// set leaf's parent field and downgrade it to a weak reference.
	
	println!("leaf parent = {:?}", leaf.parent.borrow().upgrade());
}
```