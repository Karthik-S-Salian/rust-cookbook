## Parse and increment a version string.

[![semver-badge]][semver] [![cat-config-badge]][cat-config]

Constructs a [`semver::Version`] from a string literal using [`Version::parse`],
then increments it by patch, minor, and major version number one by one.

Note that in accordance with the [Semantic Versioning Specification],
incrementing the minor version number resets the patch version number to 0 and
incrementing the major version number resets both the minor and patch version
numbers to 0.

```rust,edition2024
use semver::{BuildMetadata, Error as SemVerError, Prerelease, Version};

fn main() -> Result<(), SemVerError> {
    let mut parsed_version = Version::parse("0.2.6")?;

    assert_eq!(
        parsed_version,
        Version {
            major: 0,
            minor: 2,
            patch: 6,
            pre: Prerelease::EMPTY,
            build: BuildMetadata::EMPTY,
        }
    );

    parsed_version.patch+=1;
    assert_eq!(parsed_version.to_string(), "0.2.7");
    println!("New patch release: v{}", parsed_version);

    parsed_version.minor+=1;
    assert_eq!(parsed_version.to_string(), "0.3.7");
    println!("New minor release: v{}", parsed_version);

    parsed_version.major+=1;
    assert_eq!(parsed_version.to_string(), "1.3.7");
    println!("New major release: v{}", parsed_version);

    Ok(())
}
```

[`semver::Version`]: https://docs.rs/semver/*/semver/struct.Version.html
[`Version::parse`]: https://docs.rs/semver/*/semver/struct.Version.html#method.parse

[Semantic Versioning Specification]: http://semver.org/
