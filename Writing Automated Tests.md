# Writing Automated Tests
#rust 

## [[How to Write Tests]]

## Controlling How Tests Are Run
- `cargo test` compiles the code in test mode and runs the resulting binary.
- All tests are run in parallel by default.
- Tests can be run consecutively by changing the run command
`cargo test -- --test-threads=1`
- The same command also be used to set the number of threads for running the tests.

- By default, if a test passes, Rust's test library captures anything printed to standard output. Therefore, the output of statements like `println!` are only printed to stdout on test failure.
- This behavior can be changed by
`cargo test -- --show-output`


- To run a selection of test cases instead of the entire suite:
`cargo test <test_name / partial_test_name>`

- Tests can also be ignored by adding the `#[ignore]` attribute.
```rust
#[test]
#[ignore]
fn expensive_test() {}
```
- To run only the ignored tests,
`cargo test -- --ignored`
- To run all tests including the ignored ones,
`cargo test -- --include-ignored`

## Test Organization
### Unit Tests
- The purpose of unit tests is to test each unit of code in isolation from the rest of the code to quickly pinpoint where code is and isn’t working as expected.
- You’ll put unit tests in the _src_ directory in each file with the code that they’re testing. 
- The convention is to create a module named `tests` in each file to contain the test functions and to annotate the module with `cfg(test)`.
- `#[cfg(test)]` tells Rust to compile the test code only when `cargo test` is run.

### Integration Tests
- Integration tests are not part of the `src` files. Instead the convention is to specify them in a separated `tests` directory.
```
adder
├── Cargo.lock
├── Cargo.toml
├── src
│   └── lib.rs
└── tests
    └── integration_test.rs

```
- Integration tests do not need the `#[cfg(test)]` annotation. Cargo treats the `tests` directory specially and compiles the files in it when `cargo test` is run.
- By default, all integration tests are run. In case a single test is required,
`cargo test --test <integration_test_file>`
- As the number of integration tests grow, it can be useful to create submodules for sharing common data types and functions. Further, to prevent Rust from interpreting the common modules as individual tests, the following directory structure needs to be used.
```
├── Cargo.lock
├── Cargo.toml
├── src
│   └── lib.rs
└── tests
    ├── common
    │   └── mod.rs
    └── integration_test.rs
```