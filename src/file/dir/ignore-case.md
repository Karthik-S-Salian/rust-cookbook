## Find all files with given pattern ignoring filename case.

[![glob-badge]][glob] [![cat-filesystem-badge]][cat-filesystem]

Find all image files in the `./media/` directory matching the `img_[0-9]*.png` pattern.

A custom [`MatchOptions`] struct is passed to the [`glob_with`] function making the glob pattern case insensitive while keeping the other options [`Default`].

```rust,edition2024,no_run
use glob::{glob_with, MatchOptions};
use anyhow::Result;

fn main() -> Result<()> {
    let options = MatchOptions {
        case_sensitive: false,
        ..Default::default()
    };

    for entry in glob_with("./media/img_[0-9]*.png", options)? {
        println!("{}", entry?.display());
    }

    Ok(())
}
```

[`Default`]: https://doc.rust-lang.org/std/default/trait.Default.html
[`glob_with`]: https://docs.rs/glob/*/glob/fn.glob_with.html
[`MatchOptions`]: https://docs.rs/glob/*/glob/struct.MatchOptions.html
