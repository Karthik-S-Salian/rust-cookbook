## Convert date to UNIX timestamp and vice versa
[![chrono-badge]][chrono] [![cat-date-and-time-badge]][cat-date-and-time]

Converts a date given by [`NaiveDate::from_ymd_opt`] and [`NaiveDate::and_hms_opt`]
to [UNIX timestamp] using [`DateTime::timestamp`].
Then it calculates what was the date after one billion seconds
since January 1, 1970 0:00:00 UTC, using [`DateTime::from_timestamp`].

```rust,edition2024
use chrono::{DateTime, NaiveDate, NaiveDateTime};

fn main() {
    let date_time: NaiveDateTime = NaiveDate::from_ymd_opt(2017, 11, 12)
        .unwrap()
        .and_hms_opt(17, 33, 44)
        .unwrap();
    println!(
        "Number of seconds between 1970-01-01 00:00:00 and {} is {}.",
        date_time,date_time.and_utc().timestamp()
    );

    let date_time_after_a_billion_seconds = DateTime::from_timestamp(1_000_000_000, 0).unwrap();
    println!("Date after a billion seconds since 1970-01-01 00:00:00 was {}.",date_time_after_a_billion_seconds);
}
```

[`NaiveDate::from_ymd_opt`]: https://docs.rs/chrono/*/chrono/naive/struct.NaiveDate.html#method.from_ymd_opt
[`DateTime::from_timestamp`]: https://docs.rs/chrono/*/chrono/naive/struct.DateTime.html#method.from_timestamp
[`DateTime::timestamp`]: https://docs.rs/chrono/*/chrono/naive/struct.DateTime.html#method.timestamp
[`NaiveDate::and_hms_opt`]: https://docs.rs/chrono/*/chrono/naive/struct.NaiveDate.html#method.and_hms_opt

[UNIX timestamp]: https://en.wikipedia.org/wiki/Unix_time
