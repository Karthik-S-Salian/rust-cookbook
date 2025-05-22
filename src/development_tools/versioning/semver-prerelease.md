## Check if given version is pre-release.

[![semver-badge]][semver] [![cat-config-badge]][cat-config]

Given two versions, one is pre-release and the other is not.

```rust,edition2024
use semver::{Error as SemVerError, Version};

fn is_prerelease(version: Version) -> bool {
    !version.pre.is_empty()
}

fn main() -> Result<(), SemVerError> {
    let version_1 = Version::parse("1.0.0-alpha")?;
    let version_2 = Version::parse("1.0.0")?;

    assert!(is_prerelease(version_1));
    assert!(!is_prerelease(version_2));

    Ok(())
}
```