## Log to the Unix syslog

[![log-badge]][log] [![syslog-badge]][syslog] [![cat-debugging-badge]][cat-debugging]

Logs messages to [UNIX syslog]. Initializes logger backend
with [`syslog::init`]. [`syslog::Facility`] records the program submitting
the log entry's classification, [`log::LevelFilter`] denotes allowed log verbosity
and `Option<&str>` holds optional application name.

```rust,edition2024
# #[cfg(target_os = "linux")]
# #[cfg(target_os = "linux")]
use syslog::{Facility, Error};

# #[cfg(target_os = "linux")]
fn main() -> Result<(), Error> {
    syslog::init(Facility::LOG_USER,
                 log::LevelFilter::Debug,
                 Some("My app name"))?;
    log::debug!("this is a debug {}", "message");
    log::error!("this is an error!");
    Ok(())
}

# #[cfg(not(target_os = "linux"))]
# fn main() {
#     println!("So far, only Linux systems are supported.");
# }
```

[`log::LevelFilter`]: https://docs.rs/log/*/log/enum.LevelFilter.html
[`syslog::Facility`]: https://docs.rs/syslog/*/syslog/enum.Facility.html
[`syslog::init`]: https://docs.rs/syslog/*/syslog/fn.init.html

[UNIX syslog]: https://www.gnu.org/software/libc/manual/html_node/Overview-of-Syslog.html
