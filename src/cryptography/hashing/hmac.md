## Sign and verify a message with HMAC digest

[![ring-badge]][ring] [![cat-cryptography-badge]][cat-cryptography]

Uses [`ring::hmac`] to creates a [`hmac::Signature`] of a string then verifies the signature is correct.


```rust,edition2024
use ring::{hmac, rand};
use ring::rand::SecureRandom;
use ring::error::Unspecified;

fn main() -> Result<(), Unspecified> {
    let mut key_value = [0u8; 48];
    let rng = rand::SystemRandom::new();
    rng.fill(&mut key_value)?;
    let key = hmac::Key::new(hmac::HMAC_SHA256, &key_value);

    let message = "Legitimate and important message.";
    let signature = hmac::sign(&key, message.as_bytes());
    hmac::verify(&key, message.as_bytes(), signature.as_ref())?;

    Ok(())
}
```

[`hmac::Signature`]: https://briansmith.org/rustdoc/ring/hmac/struct.Signature.html
[`ring::hmac`]: https://briansmith.org/rustdoc/ring/hmac/
