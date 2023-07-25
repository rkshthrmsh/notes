## Fixing Ownership Errors
- Understanding why a program is unsafe is the first step in fixing it. 
- 
### Fixing an Unsafe Program: Returning a Reference to the Stack
- While trying to analyse this kind of issue, it is important to answer the following two questions:
	- How long should my variables live?
	- Who should be in charge of deallocating it?
- There are many ways of fixing such an issue and the choice is usually application dependent. What follows is an example of an issue and ways of fixing it.
```rust
// Issue: Unsafe reference return
fn return_a_string() -> &String {
	let s = String::from("Hello world");
	&s
}

// Solution 1: Move ownership out of the function.
fn return_a_string() -> String {
	let s = String::from("Hello world");
	s
}

// Solution 2: Return a string literal that lives forever.
fn return_a_string() -> &'static str {
	"Hello world"
}

// Solution 3: Defer borrow checking to runtime by using garbage collection.
use std:rc::Rc; // Reference counted pointer
fn return_a_string() -> Rc<String> {
	let s = Rc::new(String::from("Hello world"));
	Rc::clone(&s) // only clone pointer to s and not the data itself.
}

// Solution 4: Provide a "slot" to put the string using a mutable reference.
fn return_a_string(output: &mut String) {
	output.replace_range(.., "Hello world");
}
```

### Fixing an Unsafe Program: Not Enough Permissions
- Another common issue is trying to mutate read-only data or trying to drop data behind a reference.
- **Functions should not mutate their inputs if the caller would not expect it.**
- **It is ver rare for functions to take ownership of heap-owning data structures such as `Vec` and `String`**.
```rust
// Create a person's full name from a vector of name parts and add a title.
fn stringify_name_with_title(name: &Vec<String>) -> String {
	name.push(String::from("Esq")); // Needs RW permission. Has only R through &.
	let full = name.join(" ");
	full
}

// Attempt 1: Make name a &mut reference. However, this mutates input.
fn stringify_name_with_title(name: &mut Vec<String>) -> String {
	name.push(String::from("Esq"));
	let full = name.join(" ");
	full
}

// Attempt 2: Take ownership of name. Makes input unusable.
fn stringify_name_with_title(mut name: Vec<String>) -> String {
	name.push(String::from("Esq"));
	let full = name.join(" ");
	full
}

// Solution: Cloning
fn stringify_name_with_title(name: &Vec<String>) -> String {
	let mut full = name.join(" "); // Only needs R permission.
	full.push_str(" Esq.");
	full
}
```

### Fixing an Unsafe Program: Aliasing and Mutating a Data Structure
- As per the pointer safety principle, aliasing and mutating a data structure leads to indeterministic behavior.
- The solution in this case relies on one key idea: shortening the lifetime of borrows on the original variable so as to not overlap with mutations to the variable.

### Fixing an Unsafe Program: Copying vs. Moving out of a Collection
- **If a value does not own heap data, then it can be copied without a move.** For example:
	- An `i32` **does not** own heap data, so it **can** be copied without a move.
	- A `String` **does** own heap data, so it **can not** be copied without a move.
	- An `&String` **does not** own heap data, so it **can** be copied without a move.
- _Note:_ One exception to this rule is mutable references. For example, `&mut i32` is not a copyable type. So if you do something like what follows, then `a` cannot be used after being assigned to `b`. That prevents two mutable references to the same data from being used at the same time.
```rust
let mut n = 0;
let a = &mut n;
let b = a;
```
- With non-copy types like `String` we will run into what is called a **double-free**. A double-free is when a memory allocated on the heap is dropped twice. The second drop leads to undefined behavior.
- There are a few ways of dealing with this issue. Consider the following solutions for safely getting access to a non-`Copy` type like a `String` element in a `Vec`:
```rust
// Solution 1: Avoid ownership. Use immutable reference instead.
let v: Vec<String> = vec![String::from("Hello world")];
let s_ref: &String = &v[0];
println!("{s_ref}");

// Solution 2: Clone the data while leaving the vector alone.
let v: Vec<String> = vec![String::from("Hello world")];
let mut s: String = v[0].clone();
s.push('!');
println!("{s}");

// Solution 3: Use a method to remove the string out of the vector.
let mut v: Vec<String> = vec![String::from("Hello world")];
let mut s: String = v.remove(0);
s.push('!');
println!("{s}");
```

### Fixing an Unsafe Program: Mutating Different Tuple Fields
- Rust may even reject safe program because it is being conservative in evaluating borrow-checking. An example of this is in the evaluation of permission on different tuple fields.
- Rust provides fine grained tracking of permissions. For example, on the fields of a tuple.
![[Pasted image 20230609115453.png]]
- While in the example above, Rust managed to track the permissions of each tuple member separately, it may fail to do so if the code is refactored as below. Here, Rust is being conservative. Therefore inlining (as in the above snippet) is work around.
![[Pasted image 20230609115602.png]]

### Fixing an Unsafe Program: Mutating Different Array Elements
- Like in this case of tuples above, Rust's borrow-checker does fine-grained permission tracking for elements of an array.
- However, the fine-grained element-tracking is limited to a single path that represents all indexes of the array. For example, with an array `a`, the borrow-checker only tracks permission on a general element `a[_]` and not on individual paths `a[0]`, `a[1]`, etc.
- This can lead to safe programs being declared unsafe by the borrow-checker.
![[Pasted image 20230609120950.png]]
- For cases like this, Rust provides functions in the standard library. Such functions, like `split_first_mut` below, are implemented using `unsafe` blocks. Unsafe codes use "raw" pointers which are not checked for safety, and these are sometimes necessary to work around limitations of the borrow checker.
```rust
let mut a = [0, 1, 2, 3];
let (x, rest) = a.split_first_mut().unwrap(); // x ≡ a[0]
let y = &rest[0];
*x += *y;
```
