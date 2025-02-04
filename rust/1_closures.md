# Closures (questions only)

Consider the following code:

```rust
fn adder(y: usize) -> impl Fn(usize) -> usize {
    move |x| y + x
}

pub struct Wrapper<T> {
    pub elem:T
}

fn main () {
    let _wrapper = Wrapper { elem : adder(5) };
}
```

Questions:

1. What is the type of `_wrapper`?
2. What is the way to explicitly type `_wrapper` (what should be written in the place of the
   question mark `let _wrapper : Wrapper<?> = ...`)?.
3. What is the memory representation of `_wrapper`?
4. What is the difference between the type of `_wrapper` and `Wrapper<Box<dyn Fn(usize) -> usize>>`?

Answers:

1. `Wrapper<impl Fn(usize) -> usize>`
2. `_`
3. `align`, `size`: `size_of::<usize>()`
4. `_wrapper` contains a closure, but `Wrapper<Box<dyn Fn(usize) -> usize>>` contains smart pointer of a trait object
