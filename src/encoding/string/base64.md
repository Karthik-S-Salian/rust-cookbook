## Encode and decode base64

[![base64-badge]][base64] [![cat-encoding-badge]][cat-encoding]

Encodes a byte slice into a Base64 String using [`general_purpose::STANDARD.encode`]
and decodes it using [`general_purpose::STANDARD.decode`]

```rust,edition2024
use anyhow::Result;
use std::str;

use base64::{engine::general_purpose::STANDARD, Engine as _};

fn main() -> Result<()> {
    let hello = b"hello rustaceans";

    let encoded = STANDARD.encode(hello);

    let decoded = STANDARD.decode(&encoded)?;

    println!("origin: {}", str::from_utf8(hello)?);
    println!("base64 encoded: {}", encoded);
    println!("back to origin: {}", str::from_utf8(&decoded)?);

    Ok(())
}

```

[`general_purpose::STANDARD.decode`]: https://docs.rs/base64/*/base64/engine/trait.Engine.html#method.decode
[`general_purpose::STANDARD.encode`]: https://docs.rs/base64/*/base64/engine/trait.Engine.html#method.encode