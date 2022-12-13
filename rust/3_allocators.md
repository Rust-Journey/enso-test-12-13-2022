# Allocators (questions only)

Is the following code safe? If not, why and how would you fix it? (You do not have to write the fixed code, but you can if you want to).

```rust
use bumpalo::Bump;

pub type TokenVec<'s> = Vec<Token, &'s Bump>;

// We want the [`Lexer`] to own the allocator because this is an internal 
// design that should not be exposed to the users.
#[repr(C)]
pub struct Lexer<'s> {
    model: LexerModel<'s>,
    allocator: Bump,
}

impl<'s> Lexer<'s> {
    pub fn new(input: &'s str) -> Self {
        let capacity = input.len() / AVERAGE_TOKEN_LEN;
        let allocator = Bump::with_capacity(capacity);
        let allocator_ref: &Bump = unsafe { &*(&allocator as *const Bump) };
        let model = LexerModel::new(allocator_ref, &input);
        Self { allocator, model }
    }
}

pub struct LexerModel<'s> {
    pub input: &'s str,
    pub iterator: std::str::CharIndices<'s>,
    pub output: TokenVec<'s>,
    pub state: ModelState
}

impl<'s> LexerModel<'s> {
    pub fn new(allocator_ref: &'s Bump, input: &'s str) -> Self {
        let iterator = input.char_indices();
        let output = Vec::new_in(allocator_ref);
        let state = Default::default();
        Self {input, iterator, output, state}
    }
}
```


Answer:

The problem comes from this line of code in `Lexer::new()` function.
```rust
let allocator_ref: &Bump = unsafe { &*(&allocator as *const Bump) };
let model = LexerModel::new(allocator_ref, &input);
```

The `LexerModel` depends on `allocator_ref` for allocating memory for output.
But it doesn't keep any ownership or borrowing, so it allows the `allocator` in
`Lexer` struct to be dropped before `LexerModel` struct's drop.

If we move `model` of `Lexer` struct out of the `Lexer` instance after allocating,
and drop `Lexer` instance, the `model`(instance of `LexerModel`) won't be safe.

We can fix it by keeping borrowing of `allocator` of `Lexer` into `LexerModel` as 
a private field since we don't want to expose it to users.
