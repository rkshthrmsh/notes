## Using Trait Objects That Allow for Values of Different Types
- A trait object points to both an instance of a type implementing our specified trait and a table used to look up trait methods on that type at runtime.
- We can use trait objects in place of a generic or concrete type. Wherever we use a trait object, Rust’s type system will ensure at compile time that any value used in that context will implement the trait object’s trait.
- Trait objects _are_ more like objects in other languages in the sense that they combine data and behavior. But trait objects differ from traditional objects in that we can’t add data to a trait object. Trait objects aren’t as generally useful as objects in other languages: their specific purpose is to allow abstraction across common behavior.
```rust
pub trait Draw {
	fn draw(&self);
}

pub struct Screen {
	pub components: Vec<Boc<dyn Draw>>,
}
```
- This vector is of type `Box<dyn Draw>`, which is a trait object; it’s a stand-in for any type inside a `Box` that implements the `Draw` trait.
- Example of types that implement the `Draw` trait can be:
```rust
pub struct Button {
    pub width: u32,
    pub height: u32,
    pub label: String,
}

impl Draw for Button {
    fn draw(&self) {
        // code to actually draw a button
    }
}


struct SelectBox {
    width: u32,
    height: u32,
    options: Vec<String>,
}

impl Draw for SelectBox {
    fn draw(&self) {
        // code to actually draw a select box
    }
}

```
- If you’ll only ever have homogeneous collections, using generics and trait bounds is preferable because the definitions will be monomorphized at compile time to use the concrete types.
- On the other hand, with the method using trait objects, the `Screen` instance can hold different data types.
- The advantage of using trait objects and Rust’s type system to write code similar to code using duck typing is that we never have to check whether a value implements a particular method at runtime or worry about getting errors if a value doesn’t implement a method but we call it anyway. Rust won’t compile our code if the values don’t implement the traits that the trait objects need.
- One downside to using trait objects is how they interact with type inference. Type inference is trickier for trait objects. The following code will not compile because the compiler loses the information about the required type of `components` at the point where `components` is defined.
```rust
fn main() {
	let components = vec![
		Box:new(SelectBox { }),
		Box::new(Button { }),
	];
	let screen = Screen { components };
	screen.run()
}
```
- To fix the issue, you have to give a hint to the type inference algorithm. That can either be via an explicit cast on any element of the vector or it can be via a type annotation on the let-binding:
```rust
// explicit cast on an element of the vector
let components = vec![
	Box::new(SelectBox { }) as Box<dyn Draw>,
	Box::new(Button { }),
];

// type annotation on the let-binding
let components: Vec<Box<dyn Draw>> = vec![
	Box::new(SelectBox { /* .. */ }),
	Box::new(Button { /* .. */ }),
];
```

- In general, it is good to be aware that using trait objects can cause a worse developer experience for API clients in the case of type inference.
- When we use trait objects, Rust must use dynamic dispatch. The compiler doesn’t know all the types that might be used with the code that’s using trait objects, so it doesn’t know which method implemented on which type to call. Instead, at runtime, Rust uses the pointers inside the trait object to know which method to call. 
- This lookup incurs a runtime cost that doesn’t occur with static dispatch. 
- Dynamic dispatch also prevents the compiler from choosing to inline a method’s code, which in turn prevents some optimizations. 
- However, we do get extra flexibility in the code.