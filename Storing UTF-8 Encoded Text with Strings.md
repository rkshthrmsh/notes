## Storing UTF-8 Encoded Text with Strings
- Rust has only one string type in the core language, which is the string slice `str` that is usually seen in its borrowed form `&str`.
- The `String` type, which is provided by Rust’s standard library rather than coded into the core language, is a growable, mutable, owned, UTF-8 encoded string type.

### Creating a New String
```rust
// Option 1
let mut s = String::new();

// Option 2
let data = "initial contents";
let s = data.to_string();
// the method also works on a literal directly:
let s = "initial contents".to_string();

// Option 3
let s = String::from("initial contents");
```

### Updating a String
```rust
let mut s = String::from("foo");
// Option 1
s.push_str("bar");

// Option 2
s.push('l'); // adds a single character.

// Option 3
let s1 = String::from("Hello, ");
let s2 = String::from("world!");
let s3 = s1 + &s2; // s1 has been moved here and can no longer be used

// Option 4
let s1 = String::from("tic");
let s2 = String::from("tac");
let s3 = String::from("toe");

let s = format!("{}-{}-{}", s1, s2, s3);

```
- `+` uses the add method: `fn add(self, s:&str) -> String`. 
	1. `add` takes ownership of `s1`,
	2. it appends a copy of the contents of `s2` to `s1`,
	3. and then it returns back ownership of `s1`.

### Indexing into Strings
- A `String` is a wrapper over a `Vec<u8>`. Therefore, an index into the string’s bytes will not always correlate to a valid Unicode scalar value.
- Another point about UTF-8 is that there are actually three relevant ways to look at strings from Rust’s perspective: as bytes, scalar values, and grapheme clusters (the closest thing to what we would call _letters_).
- If we look at the Hindi word “नमस्ते” written in the Devanagari script, it is stored as a vector of `u8` values that looks like this:
`[224, 164, 168, 224, 164, 174, 224, 164, 184, 224, 165, 141, 224, 164, 164, 224, 165, 135]`
- That’s 18 bytes and is how computers ultimately store this data. If we look at them as Unicode scalar values, which are what Rust’s `char` type is, those bytes look like this:
`['न', 'म', 'स', '्', 'त', 'े']`
- There are six `char` values here, but the fourth and sixth are not letters: they’re diacritics that don’t make sense on their own. Finally, if we look at them as grapheme clusters, we’d get what a person would call the four letters that make up the Hindi word:
`["न", "म", "स्", "ते"]`

- A final reason Rust doesn’t allow us to index into a `String` to get a character is that indexing operations are expected to always take constant time (O(1)). But it isn’t possible to guarantee that performance with a `String`, because Rust would have to walk through the contents from the beginning to the index to determine how many valid characters there were.

- Rather than indexing using `[]` with a single number, you can use `[]` with a range to create a string slice containing particular bytes.
- The best way to operate on pieces of strings is to be explicit about whether you want bytes, characters, or graphemes.
```rust
for b in "Зд".bytes() {
    println!("{}", b);
}

for c in "Зд".chars() {
    println!("{}", c);
}
```
- Getting grapheme clusters from strings is complex, so this functionality is not provided by the standard library. Crates are available on [crates.io](https://crates.io/) for this functionality.