# About "Cookin' with Rust"

## Table of contents

- [Who this book is for](#who-this-book-is-for)
- [How to read this book](#how-to-read-this-book)
- [How to use the examples](#how-to-use-the-examples)
- [A note about error handling](#a-note-about-error-handling)
- [A note about crate representation](#a-note-about-crate-representation)

## Who this book is for

## How to read this book

## How to use the examples

## A note about error handling

Error handling in Rust is robust when done correctly, but in today's
Rust it requires a fair bit of boilerplate. Because of this one often
sees Rust examples filled with `unwrap` calls instead of proper error
handling.

Since these recipes are intended to be reused as-is and encourage best
practices, they set up error handling correctly when there are
`Result` types involved.

The basic pattern we use is to have a `fn run() -> Result` that acts
like the "real" main function. We use the [error-chain] crate to make
`?` work within `run`.

The structure generally looks like:

```rust
#[macro_use]
extern crate error_chain;

use std::net::IpAddr;
use std::str;

error_chain! {
    foreign_links {
        Utf8(std::str::Utf8Error);
        AddrParse(std::net::AddrParseError);
    }
}

fn run() -> Result<()> {
    let bytes = b"2001:db8::1";

    // Bytes to string.
    let s = str::from_utf8(bytes)?;

    // String to IP address.
    let addr: IpAddr = s.parse()?;

    println!("{:?}", addr);
    Ok(())
}

quick_main!(run);
```

This is using the `error_chain!` macro to define a custom `Error` and
`Result` type, along with automatic conversions from two standard
library error types. The automatic conversions make the `?` operator
work. The `quick_main!` macro generates the actual `main` function and
prints out the error if one occurred.

All examples in the cookbook are full self contained programs,
but for the sake of readability error handling boilerplate is hidden by default like below.
In order to read full contents click on the "expand" (<i class="fa fa-expand"></i>)
button located on snippets top-left corner.

```rust
# #[macro_use]
# extern crate error_chain;
extern crate url;

use url::{Url, Position};
#
# error_chain! {
#     foreign_links {
#         UrlParse(url::ParseError);
#     }
# }

fn run() -> Result<()> {
    let parsed = Url::parse("https://httpbin.org/cookies/set?k2=v2&k1=v1")?;
    let cleaned: &str = &parsed[..Position::AfterPath];
    println!("cleaned: {}", cleaned);
    Ok(())
}
#
# quick_main!(run);
```

For more background on error handling in Rust, read [this page of the
Rust book][error-docs] and [this blog post][error-blog].

## A note about crate representation



<!-- Links-->

[error-docs]: https://doc.rust-lang.org/book/error-handling.html
[error-blog]: https://brson.github.io/2016/11/30/starting-with-error-chain
[error-chain]: https://docs.rs/error-chain/
