# Packages, Crates, and Modules
#rust 

- Rust has a number of features that allow you to manage your code’s organization, including which details are exposed, which details are private, and what names are in each scope in your programs. These features, sometimes collectively referred to as the _module system_, include:
	- **Packages:** A Cargo feature that lets you build, test, and share crates
	- **Crates:** A tree of modules that produces a library or executable
	- **Modules** and **use:** Let you control the organization, scope, and privacy of paths
	- **Paths:** A way of naming an item, such as a struct, function, or module

## Packages and Crates
- A crate is the smallest amount of code that the Rust compiler considers at a time.
- Crates can be of two forms:
	- Binary crates: They contain a `main` function and can be compiled to an executable.
	- Library crates: Do not contain a `main` function and therefore do not compile to an executable. Instead, they define functionality that is intended to be shared.
 - A package is a bundle of one or more crates. They contain a Cargo.toml file that describes how to use the bundled crates.
 - A package can contain many binary crates but only one library crate. In case there are multiple binary crates, the files need to be placed in `src/bin`.

## Defining Modules to Control Scope and Privacy
- _Modules_ let us organize code within a crate for readability and easy reuse.
- Modules also allow us to control the _privacy_ of items, because code within a module is private by default.
- We can choose to make modules and the items within them public, which exposes them to allow external code to use and depend on them.
```
backyard
├── Cargo.lock
├── Cargo.toml
└── src
    ├── garden
    │   └── vegetables.rs
    ├── garden.rs
    └── main.rs

```
- **Start from the crate root**: When compiling a crate, the compiler first looks in the crate root file (usually _src/lib.rs_ for a library crate or _src/main.rs_ for a binary crate) for code to compile.
- **Declaring modules**: In the crate root file, you can declare new modules; say, you declare a “garden” module with `mod garden;`. The compiler will look for the module’s code in these places:
    - Inline, within curly brackets that replace the semicolon following `mod garden`
    - In the file _src/garden.rs_
    - In the file _src/garden/mod.rs_
- **Declaring submodules**: In any file other than the crate root, you can declare submodules. For example, you might declare `mod vegetables;` in _src/garden.rs_. The compiler will look for the submodule’s code within the directory named for the parent module in these places:
    - Inline, directly following `mod vegetables`, within curly brackets instead of the semicolon
    - In the file _src/garden/vegetables.rs_
    - In the file _src/garden/vegetables/mod.rs_
- **Paths to code in modules**: Once a module is part of your crate, you can refer to code in that module from anywhere else in that same crate, as long as the privacy rules allow, using the path to the code. For example, an `Asparagus` type in the garden vegetables module would be found at `crate::garden::vegetables::Asparagus`.
- **Private vs public**: Code within a module is private from its parent modules by default. To make a module public, declare it with `pub mod` instead of `mod`. To make items within a public module public as well, use `pub` before their declarations.
- **The `use` keyword**: Within a scope, the `use` keyword creates shortcuts to items to reduce repetition of long paths. In any scope that can refer to `crate::garden::vegetables::Asparagus`, you can create a shortcut with `use crate::garden::vegetables::Asparagus;` and from then on you only need to write `Asparagus` to make use of that type in the scope.

## Paths for Referring to an Item in the Module Tree
- A path can take two forms:
	- An _absolute path_ is the full path starting from a crate root; for code from an external crate, the absolute path begins with the crate name, and for code from the current crate, it starts with the literal `crate`.
	- A _relative path_ starts from the current module and uses `self`, `super`, or an identifier in the current module.
- The preference in general is to specify absolute paths because it’s more likely we’ll want to move code definitions and item calls independently of each other.
- Items in a parent module can’t use the private items inside child modules, but items in child modules can use the items in their ancestor modules. This is because child modules wrap and hide their implementation details, but the child modules can see the context in which they’re defined. A child can access an item in the parent module using `super`.
- Making the module public doesn’t make its contents public. The `pub` keyword on a module only lets code in its ancestor modules refer to it, not access its inner code. Because modules are containers, there’s not much we can do by only making the module public; we need to go further and choose to make one or more of the items within the module public as well.
- The default for enum variants is to be public. Structs are often useful without their fields being public, so struct fields follow the general rule of everything being private by default unless annotated with `pub`.

## Bringing Paths into Scope with the `use` Keyword
- Adding `use` and a path in a scope is similar to creating a symbolic link in the filesystem. Paths brought into scope with `use` also check privacy, like any other paths.
- Note that `use` only creates the shortcut for the particular scope in which the `use` occurs.
- Bringing the function's parent module into scope is the idiomatic way of bringing a function into scope. This means we have to specify the parent module when calling the function. Specifying the parent module when calling the function makes it clear that the function isn’t locally defined while still minimizing repetition of the full path.
- When we bring a name into scope with the `use` keyword, the name available in the new scope is private. To enable the code that calls our code to refer to that name as if it had been defined in that code’s scope, we can combine `pub` and `use`. This technique is called _re-exporting_ because we’re bringing an item into scope but also making that item available for others to bring into their scope.
- Re-exporting is useful when the internal structure of your code is different from how programmers calling your code would think about the domain.
- With `pub use`, we can write our code with one structure but expose a different structure. Doing so makes our library well organized for programmers working on the library and programmers calling the library.