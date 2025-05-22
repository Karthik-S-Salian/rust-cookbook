## Handle errors correctly in main

[![anyhow-badge]][anyhow] [![cat-rust-patterns-badge]][cat-rust-patterns]
[![thiserror-badge]][thiserror] [![cat-rust-patterns-badge]][cat-rust-patterns]
[![error-chain-badge]][error-chain] [![cat-rust-patterns-badge]][cat-rust-patterns]

# Error Strategies (2024)

As recommended in Rust by Example, [`Box`ing errors] is seen as an easy
strategy for getting started.

```rust,edition2024
Box<dyn Error>
````

To understand what kind of error handling may be required study [Designing 
error types in Rust] and consider [`thiserror`] for libraries or [`anyhow`] as 
a maintained error aggregation option.

```rust,edition2024
use thiserror::Error;

#[derive(Error,Debug)]
pub enum MultiError {
  #[error("🦀 got {0}")]
  ErrorClass(String),
}
```

# Anyhow 
<!-- TODO anyhow explaination -->
Application authors can compose enums using `anyhow` can import the `Result`
type from the crate to provide auto-`Box`ing behavior

```rust,edition2024,ignore
use std::fs::File;
use std::io::Read;
use anyhow::{Result, Context};

fn read_uptime() -> Result<u64> {
    let mut uptime = String::new();

    File::open("/proc/uptime")
        .context("Failed to open /proc/uptime")?
        .read_to_string(&mut uptime)
        .context("Failed to read from /proc/uptime")?;
    
    let seconds = uptime
        .split('.')
        .next()
        .context("Cannot parse uptime data")?
        .parse::<u64>()
        .context("Failed to parse uptime as u64")?;

    Ok(seconds)
}

fn main() -> Result<()> {
    match read_uptime() {
        Ok(uptime) => println!("uptime: {} seconds", uptime),
        Err(err) => eprintln!("error: {:?}", err),
    }
    Ok(())
}
```

[`anyhow`]: https://docs.rs/anyhow/latest/anyhow/
[`error_chain!`]: https://docs.rs/error-chain/*/error_chain/macro.error_chain.html
[`Error`]: https://doc.rust-lang.org/std/error/trait.Error.html
[`foreign_links`]: https://docs.rs/error-chain/*/error_chain/#foreign-links
[`std::io::Error`]: https://doc.rust-lang.org/std/io/struct.Error.html
[`thiserror`]: https://docs.rs/thiserror/latest/thiserror/

[handle errors in Rust]: https://doc.rust-lang.org/book/second-edition/ch09-00-error-handling.html
[`Box`ing errors]: https://doc.rust-lang.org/rust-by-example/error/multiple_error_types/boxing_errors.html
[Designing error types in Rust]: https://mmapped.blog/posts/12-rust-error-handling
