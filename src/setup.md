# Setup

Before getting into the more theoretical parts of grammars and APIs, let's first
make sure we're all set up.

## Rust and Cargo

The easiest way to install Rust and Cargo together is to follow the instructions
on [rustup.rs](https://rustup.rs). Once that is out of the way, make sure you
add *pest* to your `Cargo.toml`:

```toml
pest = "^1.0.0-beta"
pest_derive = "^1.0.0-beta"
```

*pest_derive* is the part of the parser that analyzes, verifies, optimizes, and
generates the code that then makes use of the APIs found in the *pest* crate.
This is separate because the actual procedural macro that derives the parser for
you is linked at compile time.



## The `.pest` grammar file

The actual grammar gets saved in separate `.pest` files, relative to Cargo's
`src` directory. They are then used in order to derive an implementation of the
[Parser](https://docs.rs/pest/1.0.0-beta/pest/trait.Parser.html) trait.

Due to the fact that procedural macro do not offer an API to tell the compiler
which files are relevant to compilation, it is necessary to provide a small hint
in the form of a debug-only `const` in order to make sure that your grammar gets
recompiled after every change.

So, to define an unimaginatively but appropriately-named parser, you should add
the following code to the Rust file where you want the parser to be.

```rust
#[cfg(debug_assertions)]
const _GRAMMAR: &'static str = include_str!("path/to/my_grammar.pest"); // relative to this file

#[derive(Parser)]
#[grammar = "path/to/my_grammar.pest"] // relative to src
struct MyParser;
```

Also, don't forget to add the crate dependency in your crate's main file.

```rust
extern crate pest;
#[macro_use]
extern crate pest_derive;
```
