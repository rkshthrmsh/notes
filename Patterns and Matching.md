# Patterns and Matching
#rust 

- A pattern consists of some combination of the following:
	- Literals
	- Destructured arrays, enums, structs, or tuples
	- Variables
	- Wildcards
	- Placeholders

## All the Places Patterns Can Be Used
- `match` arms. The use of patterns in `match` arms needs to be exhaustive—all possibilities must be accounted for.
- `if let` expressions.
- `while let` conditional loops.
- `for` loops, in the value that directly follows the `for` keyword.
- `let` statements
```rust
let x = 6; // represents let PATTERN = EXPRESSION
let (a, b) = (1, 2);
```
- Function and closure parameters can also be patterns
```rust
fn test(&(x, y): &(i32, i32)) { }
```

## Refutability: Whether a Pattern Might Fail to Match
- Patterns that will match for any possible value passed are _irrefutable_.
- Patterns that can fail to match for some possible value are _refutable_. Here are some examples:
	- In the expression `if let Some(x) = a_value`, then `Some(x)` is refutable. If the value in the `a_value` variable is `None` rather than `Some`, the `Some(x)` pattern will not match.
	- In the expression `let &[x, ..] = a_slice`, then `&[x, ..]` is refutable. If the value in the `a_slice` variable has zero elements, the `&[x, ..]` pattern will not match.
 - Function parameters, `let` statements, and `for` loops can only accept irrefutable patterns, because the program cannot do anything meaningful when values don’t match.
 
## [Pattern Syntax](https://rust-book.cs.brown.edu/ch18-03-pattern-syntax.html#pattern-syntax)