# Interacting with Data

In Rust, there are two main ways to interact with data: owned types and references.

1. **Owned Types**: When a variable is initialized with some data, it 'owns that data. This means that it is responsible for the cleanup of the data when it goes out of scope.

- Ownership can be transferred to another variable using `move` semantics. Once the data has been **moved**, the previous variable can _no longer be used_.

```rust
let s1 = String::from("hello"); // s1 owns the data
let s2 = s1; // ownership is moved from s1 to s2
println!("{}", s1); // This would be an error! s1 no longer owns the data
```

2. **References**: References allow you to refer some to data _without taking ownership*. This is useful when you wanto to access data without taking responsibility for cleaning it up. 

- References are created using the `&` symbol.

There are two types of references in Rust: `mutable` and `immutable`. You can have multiple immutable references or one mutable reference at a time inside a scope.

```rust
let s1 = String::from("hello"); // s1 owns the data
let len = calculate_length(&s1); // s1 is borrowed as an immutable reference

fn calculate_length(s: &String) -> usize { // s is a reference to a String
    s.len()
} // Here, s goes out of scope. But because it does not have ownership of the data, nothing happens
```

In this example, `calculate_length` has a reference to `s1`, but it does not own it, so when `calculate_length` is finished, it does not deallocate memory of s1.
