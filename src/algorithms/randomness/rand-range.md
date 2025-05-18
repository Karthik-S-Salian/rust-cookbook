## Generate random numbers within a range

[![rand-badge]][rand] [![cat-science-badge]][cat-science]

Generates a random value within range  with [`Rng::random_range`].
high is excluded when low..high and included when low..=high.
low is included in both cases.

```rust,edition2024
use rand::Rng;

fn main() {
    let mut rng = rand::rng();
    println!("Integer: {}", rng.random_range(0..10));
    println!("Integer with high included: {}", rng.random_range(0..=10));
    println!("Float: {}", rng.random_range(0.0..10.0));
}
```

[`Uniform`] can obtain values with [uniform distribution].
This has the same effect, but may be faster when repeatedly generating numbers
in the same range.

```rust,edition2024
use rand::distr::{Distribution, Uniform};

fn main() {
    let die = Uniform::try_from(1..=6).unwrap();
    let mut rng = rand::rng();

    loop {
        let throw = die.sample(&mut rng);
        println!("Roll the die: {}", throw);
        if throw == 6 {
            break;
        }
    }
}
```

[`Uniform`]: https://docs.rs/rand/*/rand/distributions/uniform/struct.Uniform.html
[`Rng::random_range`]: https://doc.rust-lang.org/rand/*/rand/trait.Rng.html#method.random_range
[uniform distribution]: https://en.wikipedia.org/wiki/Uniform_distribution_(continuous)
