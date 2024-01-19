# Iterating/Modifying the contents of a vector

In rust, there are two common ways of iterating over a vector and modifying it's context. Their use depends on the context:

## Regular style

The `vec_loop` function uses a mutable reference to **directly modify** the elements of the vector in-place. This is useful when you want to change the original vector without creating a new one

```rust
fn vec_loop(mut v: Vec<i32>) -> Vec<i32> {
    for element in v.iter_mut() {
        // TODO: Fill this up so that each element in the Vec `v` is
        // multiplied by 2.
        *element = *element * 2;
    }

    // At this point, `v` should be equal to [4, 8, 12, 16, 20].
    v
}
```



## Functional Programming style

The `vec_map` function, on the other hand, **creates a new vector** with the modified elements. This is a more functional programming style and is useful when you want to keep the original vector unchanged.

```rust
fn vec_map(v: &Vec<i32>) -> Vec<i32> {
    v.iter().map(|element| {
        // TODO: Do the same thing as above - but instead of mutating the
        // Vec, you can just return the new number!
        return element*2;
    }).collect()
}
```
