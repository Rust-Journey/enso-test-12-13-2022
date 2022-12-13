# Macros (writing a code)

Write a macro `to_angles` by using `macro_rules!`, which will replace every occurence of `[<[ ... ]>]` with `< ... >`. Also, please explain what such a macro could be used for and what advantages and disadvantages its usage will introduce. You can use unstable Rust if you want to. For example, for the given input:

```rust
to_angles! {
    struct Token [<[T]>] {
        prefix: Option [<[T]>],
        data: String,
        suffix: Option [<[T]>],
    }
}
```

it should produce:

```rust
struct Token<T> {
    prefix: Option<T>,
    data: String,
    suffix: Option<T>,
}
```

Answer:
https://github.com/Rust-Journey/macros
