## What is Ownership?
- Ownership ensures safety of Rust programs. This ensures that programs never have undefined behavior.
- Rust prevents undefined behavior at compile-time instead of run-time. This means that bugs are caught before production, improving the reliability of the software. Catching bugs at compile-time also means that fewer expensive run-time checks are necessary.

### Rust Memory Model
- Variables live in frames, which are a mapping from variables to values within a single scope.
![[Pasted image 20230608120423.png]]
- Frames are organized into a **stack** of currently-called-function.
- Once a function returns, Rust deallocates the function's frame. This is also called **freeing** or **dropping**.
- When an expression reads a variable, the variable's value is copied from its slot in the stack frame.
- Rust also provides **pointers** for transferring access to data without copying it since copying can sometimes be expensive. One common way of making pointers is to allocate memory on the **heap** which is a region of memory where data can live indefinitely. Data on the heap is not tied down to any specific task frame.
- **Rust does not permit manual memory management**. Instead memory is managed automatically.
- **Box deallocation principle**: If a variable owns a box, when Rust deallocates the variable's frame, then Rust deallocates the box's heap memory.
```rust
let a = Box::new([0; 1_000_000]); // create a new Box under a's ownership.
let b = a; // ownership of Box (heap) transferred to b.
```
- **Moved heap data principle**: If a variable `x` moves ownership of heap data to another variable `y`, the `x` cannot be used after the move. This avoids undefined behavior from reading deallocated memory.
![[Pasted image 20230608124012.png]]
- **Cloning avoids moves**: `.clone()` is a method that can be used to avoid moving data.
![[Pasted image 20230608124039.png]]

### Summary
- All heap data must be owned by exactly one variable.
- Rust deallocates heap data once its owner goes out of scope.
- Ownership can be transferred by moves, which happen on assignments and function calls.
- Heap data can only be accessed through its current owner, not a previous owner.