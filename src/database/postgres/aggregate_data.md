## Aggregate data

[![postgres-badge]][postgres] [![cat-database-badge]][cat-database]

This recipe lists the nationalities of the first 7999 artists in the database of the [`Museum of Modern Art`] in descending order.

```rust,edition2024,no_run
use postgres::{Client, Error, NoTls};

struct Nation {
    nationality: String,
    count: i64,
}

fn main() -> Result<(), Error> {
    let mut client = Client::connect(
        "postgresql://karthikssalian:password@localhost:5432/booktest",
        NoTls,
    )?;

    client.batch_execute(
        "
        CREATE TABLE IF NOT EXISTS artists (
            id SERIAL PRIMARY KEY,
            nationality VARCHAR NOT NULL
        );
        ",
    )?;

    client.batch_execute(
        "
        INSERT INTO artists (nationality) VALUES
            ('American'),
            ('French'),
            ('Japanese')
        ON CONFLICT DO NOTHING;
        ",
    )?;

    for row in client.query(
        "SELECT nationality, COUNT(nationality) AS count
         FROM artists
         GROUP BY nationality
         ORDER BY count DESC",
        &[],
    )? {
        let nationality: Option<String> = row.get(0);
        let count: Option<i64> = row.get(1);

        if let (Some(nat), Some(cnt)) = (nationality, count) {
            let nation = Nation {
                nationality: nat,
                count: cnt,
            };
            println!("{} {}", nation.nationality, nation.count);
        }
    }

    Ok(())
}
```

[`Museum of Modern Art`]: https://github.com/MuseumofModernArt/collection/blob/main/Artists.csv
