# Object-Oriented Programming Features of Rust
#rust 

## Characteristics of Object-Oriented Languages
- Although there is no consensus in the programming community about what features make up an OOP language, there are certain common features among OOP languages. These include objects, encapsulation, and inheritance.

### Objects
- An object packages both data and the procedures that operate on that data. The procedures are typically called methods or operations. Based on this definition, Rust is object-oriented because of data constructs such as structs and enums, and the `impl` blocks.

### Encapsulation
- Encapsulation means that the implementation details of an object aren't accessible to code using that object. In Rust, the `pub` keyword can be used to decide which modules, types, functions, and methods in our code should be public. By default, everything is private.

### Inheritance
- Inheritance is a mechanism whereby an object can inherit elements from another object's definition, thus gaining the parent object's data and behavior without needing to define them again. Rust does not support inheritance.
- Typically, inheritance is chosen for two main reasons:
	- Code reuse: Code reuse can be partially implemented in Rust using traits with default method implementations.
	- Polymorphism: Polymorphism is a general concept that refers to code that can work with data of multiple types. Rust uses generics to abstract over different possible types and trait bounds to impose constraints on what those types must provide. This is sometimes called *bounded parametric polymorphism*.

## [[Trait Objects|Using Trait Objects That Allow for Values of Different Types]]