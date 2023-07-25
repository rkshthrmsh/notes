## Ownership Recap
### Ownership versus Garbage Collection
- Most programming languages use a garbage collector to manage memory.
- A garbage collector runs adjacent to a running program and scans the memory to find data that are no longer used and frees them.
- The benefit of a garbage collector is that it avoids undefined behaviour. 
- However, **garbage collection can be unpredictable**. This is because garbage collection makes it difficult to see how the pointers are interacting under the hood.

- Rust's ownership system, on the other hand, puts pointers front-and-center. This has the benefit of:
	- improving runtime performance by avoiding garbage collection.
	- improving predictability by preventing accidental "leaks" of data.

### [The Concepts of Ownership](https://rust-book.cs.brown.edu/ch04-05-ownership-recap.html#the-concepts-of-ownership)