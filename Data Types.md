## Data Types
- Every value is Rust is of a certain data type.
- Rust is a statically typed language. Therefore, type annotations are needed to resolve any ambiguities before compile time.

### Scalar Types
- Four primary scalar types: integers, floating-point numbers, Boolean, and characters.

#### Integer Types
- Integer type representations contain information on whether they are signed (`i`) or unsigned (`u`), and the number of bits (8, 16, 32, 64, or 128) in the representation. 
- Special types `isize` and `usize` express signed and unsigned integers whose representation contains a number of bits specific to the processor architecture. The primary use case is to index collections.
- Integer literals can be expressed in various systems: decimal (`1000` or `1_000`), hexadecimal (`0xff`), octal (`0o77`), binary (`0b1111_0000`), and byte (`b'A'` in `u8` only).
- Rust defaults to `i32` for integer types.

##### Integer Overflow
- In debug mode, Rust includes checks for integer overflow that causes the program to panic.
- However, in release mode, checks for integer overflow are excluded. Instead, the variable wraps around.
- Overflow can be handled by using methods provided by the standard library:
	- Wrap in all modes with the `wrapping_*` methods, such as `wrapping_add`.
	- Return the `None` value if there is overflow with the `checked_*` methods.
	- Return the value and a boolean indicating whether there was overflow with the `overflowing_*` methods.
	- Saturate at the value’s minimum or maximum values with the `saturating_*` methods.

#### Floating-Point Types
- Two primitive types: `f32` and `f64` (default). All floating-point types are signed.
- Floating-point numbers are represented according to IEEE-754 standard.

#### The Boolean Type
- Specified using `bool`. 
- Two possible values: `true` and `false`.
- Booleans are 1 byte in size.

#### The Character Type
- Specified with single quotes. String literals use double quotes.
- 4 bytes in size—can represent a large number of characters / symbols.

### Compound Types
- Two primitive compound types: tuples and arrays.

#### The Tuple Type
- Collection of  values with a variety of types. 
- Immutable length.
- A tuple variable can be destructured to retrieve values.
- A tuple can also be indexed into.
```rust
let tup: (i32, f64, u8) = (500, 6.4, 1); // optional type annotation included
let (x, y, z) = tup; // destructuring
let five_hundred = tup.0; // indexing
```
- The tuple without any values `()` is called a *unit*.

#### The Array Type
- Unlike a tuple, every element must have the same type.
- Arrays also have a fixed length. Because the size is fixed and known, arrays can be allocated on the stack instead of the heap.
```rust
let a = [1, 2, 3, 4];
let a: [i32, 4] = [1, 2, 3, 4]; // type annotated
let a = [3; 5]; // let a = [3, 3, 3, 3, 3];
let x = a[0]; // indexing
```