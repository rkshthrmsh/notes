# Common Collections
#rust 

## Storing Lists of Values with Vectors
- Vectors allow you to store more than one value in a single data structure that puts all the values next to each other in memory. Vectors can only store values of the same type.
- To create a vector and then add elements to it, we can use the `push` method.
- There are two ways to reference a value stored in a vector: via indexing or using the `get` method. When we use the `get` method with the index passed as an argument, we get an `Option<&T>` that we can use with `match`.
- The `next` method advances the iterator and returns an optional reference to the previous element, either `Some` (which we unwrap) or `None` at the end of the vector.
- We can define an enum whose variants will hold the different value types, and all the enum variants will be considered the same type: that of the enum. Then we can create a vector to hold that enum and so, ultimately, holds different types.

## [[Storing UTF-8 Encoded Text with Strings]]

## Storing Keys with Associated Values in Hash Maps
- The type `HashMap<K, V>` stores a mapping of keys of type `K` to values of type `V` using a _hashing function_, which determines how it places these keys and values into memory.
- One way to create an empty hash map is using `new` and adding elements with `insert`.
```rust
// use is needed because HashMaps are not included in preclude
use std::collections::HashMap;

let mut scores = HashMap::new();
scores.insert(String::from("Blue"), 10);
scores.insert(String::from("Yellow"), 50);
```
- Just like vectors, hash maps store their data on the heap. Like vectors, hash maps are homogeneous: all of the keys must have the same type as each other, and all of the values must have the same type.
- We can get a value out of the hash map by providing its key to the `get` method. The `get` method returns an `Option<&V>`; if there’s no value for that key in the hash map, `get` will return `None`. This program handles the `Option` by calling `copied` to get an `Option<i32>` rather than an `Option<&i32>`, then `unwrap_or` to set `score` to zero if `scores` doesn't have an entry for the key.
```rust
let team_name = String::from("Blue");
let score = scores.get(&team_name).copied().unwrap_or(0);
```
- We can iterate over each key/value pair in a hash map in a similar manner as we do with vectors, using a `for` loop.
```rust
for (key, value) in &scores {
	println!("{}: {}", key, value);
}
```
- For types that implement the `Copy` trait, like `i32`, the values are copied into the hash map. For owned values like `String`, the values will be moved and the hash map will be the owner of those values.
- If we insert a key and a value into a hash map and then insert that same key with a different value, the value associated with that key will be replaced.

### Adding a Key and Value Only If a Key Isn't Present
- Hash maps have a special API for this called `entry` that takes the key you want to check as a parameter. The return value of the `entry` method is an enum called `Entry` that represents a value that might or might not exist.
```rust
use std::collections::HashMap;

let mut scores = HashMap::new();
scores.insert(String::from("Blue"), 10);

scores.entry(String::from("Yellow")).or_insert(50);
scores.entry(String::from("Blue")).or_insert(50); // no change

println!("{:?}", scores);

```

 - By default, `HashMap` uses a hashing function called _SipHash_ that can provide resistance to Denial of Service (DoS) attacks involving hash tables. This is not the fastest hashing algorithm available, but the trade-off for better security that comes with the drop in performance is worth it. If you profile your code and find that the default hash function is too slow for your purposes, you can switch to another function by specifying a different hasher.