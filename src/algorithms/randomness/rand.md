## Generate random numbers

[![rand-badge]][rand] [![cat-science-badge]][cat-science]

Generates random numbers with help of random-number generator [rand::Rng](https://docs.rs/rand/*/rand/trait.Rng.html) obtained via [rand::rng](https://docs.rs/rand/*/rand/fn.rng.html). 
Integers are uniformly distributed over the range of the type, and floating point numbers are uniformly distributed from 0 up to but not including 1.

```rust,edition2024
use rand::Rng;

fn main() {
    let mut rng = rand::rng();

    let n1: u8 = rng.random();
    let n2: u16 = rng.random();
    println!("Random u8: {}", n1);
    println!("Random u16: {}", n2);
    println!("Random u32: {}", rng.random::<u32>());
    println!("Random i32: {}", rng.random::<i32>());
    println!("Random float: {}", rng.random::<f64>());
}
```
