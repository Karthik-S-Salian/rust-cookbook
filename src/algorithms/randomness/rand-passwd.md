## Create random passwords from a set of alphanumeric characters

[![rand-badge]][rand] [![cat-os-badge]][cat-os]

Randomly generates a string of given length ASCII characters in the range `A-Z,
a-z, 0-9`, with [`Alphanumeric`] sample.

```rust,edition2024
use rand::{rng, Rng};
use rand::distr::{Alphanumeric, SampleString};

fn main() {
    let rand_string: String = rng()
        .sample_iter(&Alphanumeric)
        .take(30)
        .map(char::from)
        .collect();

    println!("Random string:{}", rand_string);

    // The SampleString trait provides an easier method of generating a random String, 
    // and offers more efficient allocation
    let string = Alphanumeric.sample_string(&mut rand::rng(), 16);
    println!("Random string: {}", string);
}
```

[`Alphanumeric`]: https://docs.rs/rand/*/rand/distributions/struct.Alphanumeric.html
