# Ownership and Borrowing

One of Rust's main appeals is its explicit and safer memory management (compared to C and C++). Some of it's key features are:

- No implicit casting
- No null pointers
- No data races

Rust has an ownership system, which ensures that you're always aware of when and where memory is allocated and deallocated.

## Ownership

Each value in Rust has a variable that's called its owner. There can only be `one owner at a time`. When the owner goes out of scope, the value will be dropped (freed).

## Borrowing

Instead of transferring ownership, Rust allows you to borrow a reference to a value. Borrowing is subject to two rules:

1. You can have `one mutable reference`

2. You can have `any number of inmutable references` at a time.

Here is a simple example of borrowing a variable by creating a reference to it:

```rust
fn main() {
    let s1 = String::from("hello");
    let len = calculate_length(&s1);
    println!("The length of '{}' is {}.", s1, len);
}

fn calculate_length(s: &String) -> usize {
    s.len()
}

```
In this example, `s1` is a `String` that owns its data. When we call `calculate_length(&s1)`, we're creating a *reference* to s1 (we're borrowing s1) and passing it to `calculate_length`. 

Inside `calculate_length`, `s` is a reference to a String, so it doesn't own its data.

When `calculate_length` returns, `s` goes out of scope, but because it doesn't own its data, nothing is dropped. `s1` continues to be valid for the rest of `main`, and it's dropped when main returns.

Note that while `s1` is borrowed, it's `immutable`. If you want to mutate `s1` while it's borrowed, you can use a mutable reference `(&mut s1)`. However, you can only have one mutable reference to a particular piece of data in a particular scope. This restriction allows Rust to prevent data races at compile time.
