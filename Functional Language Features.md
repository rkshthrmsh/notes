# Functional Language Features: Iterators and Closures
#rust 

## [[Closures]]

## Processing a Series of Items with Iterators
- Iterators allow performing a task on a sequence of items in turn. 
- In Rust, iterators are lazy—they have no effect until methods calling the iterator use them up.
- All iterators implement the `Iterator` trait. This trait requires implementors to define one method: `next`, which returns one item of the iterator wrapped in `Some` and returns `None` when the iterator is exhausted.
- The standard library provides many different ways of creating iterators.
```rust
iter() // creates immutable references to the values in the iterable
iter_mut() // iterates over mutable references
into_iter() // takes ownership of iterable and returns owned values
```

- *Consuming adapters* are methods that use up the iterator. One example is the `sum` method.
- *Iterator adapters* are methods that don't consume the iterator, but produce different iterators by changing some aspect of the original iterator. An example of an iterator adapter is `map`, which takes a closure and calls it on each item of the iterable. However, since iterators are lazy, a consuming adapter is required to get results from calls to iterator adapters.
```rust
let v1: Vec<i32> = vec![1, 2, 3];
let v2: Vec<_> = v1.iter().map(|x| x + 1).collect() // collect is a consumer
assert_eq!(v2, vec![2, 3, 4]);
```

- Many iterators take closures as arguments, and commonly, these closures capture their environment. In the following example, the `filter` method takes a closure which returns a boolean value based on which the iterator values will be included (for `true`) or excluded (for `false`).
```rust
#[derive(PartialEq, Debug)]
struct Shoe {
	size: u32,
	style: String,
}

fn shoes_in_size(shoes: Vec<Shoe>, shoe_size: u32) -> Vec<Shoe> {
	shoes.into_iter().filter(|s| s.size == shoe_size).collect()
}
```