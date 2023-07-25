## References and Borrowing
- Although ownership, boxes, and moves provide a safe programming paradigm, they can be inconvenient to use. Examples include situations in which variables allocated on the heap (such as strings) need to be ready twice. Although this can be circumvented by copying the heap variables, it leads to code that is quite verbose.
- Rust provides references to solve this issue. **References** are non-owning pointers. They do not own the data they point to.
```rust
// Example of inconvenience
fn main() {
	let m1 = String::from("hello");
	let m2 = String::from("world");
	greet(m1, m2);
	let s = format!("{} {}", m1, m2); // Error: m1 and m2 have moved
}

fn greet(g1: String, g2: String) {
	println!("{} {}!", g1, g2); // This is where the ownership moved
}

// Repeated with references
fn main() {
	let m1 = String::from("hello");
	let m2 = String::from("world");
	greet(&m1, &m2); // ampersands are used for referencing
	let s = format!("{} {}", m1, m2);
}

fn greet(g1: &String, g2: &String) { // ammpersands used for referencing
	println!("{} {}!", g1, g2);
}
```

### Dereferencing a Pointer
- The **dereference** operator (\*) follows a pointer to its data.
![[Pasted image 20230608145424.png]]

### Rust Avoids Simultaneous Aliasing and Mutation
- Aliasing is accessing the same data through different variables.
- When combined with mutation, aliasing can be a recipe for disaster:
	- Data can be deallocated, leaving the other variable pointing to deallocated memory.
	- Data can be mutated, invalidating expected runtime properties.
	- Concurrent mutations could lead to race conditions with nondeterministic behavior. 
 ![[Pasted image 20230608151816.png]]
 - **Pointer Safety Principle**: Data should never be aliased and mutated at the same time.

### Borrower Checker
- The borrower checker ensures the safety of references. The core idea behind it is that variables have three kinds of permissions.
	- **Read** (R): data can be copied to another location.
	- **Write** (W): data can be mutated in-place.
	- **Own** (O): data can be moved or dropped.
- These permissions don't exist at runtime, only in the compiler.
- The key idea behind references in Rust is that they can **temporarily remove these permissions**.
![[Pasted image 20230608172523.png]]
- `*num` is seen along with `num` because these permission are mutually exclusive. Accessing data through a reference is different to manipulating the reference itself.
- Permissions are defined on **paths** and not just variables. A Path is anything on the left-hand side of an assignment.

#### Mutable References Provide Unique and Non-Owning Access to Data
- References discussed in the previous sections are what are called **immutable references** or **shared references**. These permit aliasing but disallow mutation.
- Sometimes it may be useful to provide temporary mutable access to data without moving it. Such references are called **mutable references** or **unique references** and are defined by changing the type to `&mut`.
![[Pasted image 20230608211857.png]]
- Two points worth noting here are:
	- Unlike in immutable reference above, `num` loses all its permissions, including R when a mutable reference is defined. This makes mutable references safe—they allow mutation but prevent aliasing.
	- In addition, the `*num` path gains a W permission. With this permission, the path can now be useful for mutating the data.

#### Data Must Outlive All Of Its References
- From the borrow checker's perspective, data must outlive any references to it. It is easy to determine the lifetime of variables in code where Rust is aware of how long references live.
- However, a different mechanism is needed when Rust does not have lifetime information. This happens when references are either input to, or output from, a function.
- Rust achieves this by introducing a new kind of permission called the flow permission (F). Unlike RWO, F does not change throughout the function body. 
![[Pasted image 20230608215346.png]]
- In the above example, since Rust can determine the *flow* from the input to the output reference, the function compiles without any issues.
- On the other hand, in the example below, the flow is not deterministic. This is because, in a higher function, `default` could be mutated before the lifetime of this functions return. An example of this is below the snippet.
![[Pasted image 20230608215556.png]]
```rust
fn main() {
	let strings = vec![];
	let default = String::from("default");
	let s = first_or(&strings, &default); // Rust unsure what flows to s
	drop(default); // if default had flown,
	println!("{}", s); // this statement becomes undeterministic.
}
```