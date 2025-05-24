## Consume a paginated RESTful API

[![reqwest-badge]][reqwest] [![serde-badge]][serde] [![cat-net-badge]][cat-net] [![cat-encoding-badge]][cat-encoding]

Wraps a paginated web API in a convenient Rust iterator. The iterator lazily
fetches the next page of results from the remote server as it arrives at the end of each page.

```rust,edition2024,no_run
use reqwest::blocking::Client;
use reqwest::header::{USER_AGENT, ACCEPT};
use serde::Deserialize;
use std::error::Error;

#[derive(Debug, Deserialize)]
struct Release {
    tag_name: String,
    name: Option<String>,
    draft: bool,
    prerelease: bool,
}

struct GitHubReleases {
    owner: String,
    repo: String,
    client: Client,
    page: u32,
    per_page: u32,
    done: bool,
    buffer: std::vec::IntoIter<Release>,
}

impl GitHubReleases {
    fn new(owner: &str, repo: &str) -> Self {
        GitHubReleases {
            owner: owner.to_string(),
            repo: repo.to_string(),
            client: Client::new(),
            page: 0,
            per_page: 5,
            done: false,
            buffer: vec![].into_iter(),
        }
    }

    fn try_next(&mut self) -> Result<Option<Release>, Box<dyn Error>> {
        if let Some(next) = self.buffer.next() {
            return Ok(Some(next));
        }

        if self.done {
            return Ok(None);
        }

        self.page += 1;
        let url = format!(
            "https://api.github.com/repos/{}/{}/releases?per_page={}&page={}",
            self.owner, self.repo, self.per_page, self.page
        );

        println!("Fetching: {}", url);

        let response = self
            .client
            .get(&url)
            .header(USER_AGENT, "cookbook agent")
            .header(ACCEPT, "application/vnd.github+json")
            .send()?;

        if response.status().as_u16() == 404 {
            return Err("Repository not found".into());
        }

        let releases: Vec<Release> = response.json()?;

        if releases.is_empty() {
            self.done = true;
            return Ok(None);
        }

        self.buffer = releases.into_iter();
        Ok(self.buffer.next())
    }
}

impl Iterator for GitHubReleases {
    type Item = Result<Release, Box<dyn Error>>;

    fn next(&mut self) -> Option<Self::Item> {
        match self.try_next() {
            Ok(Some(release)) => Some(Ok(release)),
            Ok(None) => None,
            Err(e) => Some(Err(e)),
        }
    }
}

fn main() -> Result<(), Box<dyn Error>> {
    let mut releases = GitHubReleases::new("rust-lang", "rust");

    for release in &mut releases {
        let release = release?;
        println!(
            "Tag: {}, Name: {}, Draft: {}, Prerelease: {}",
            release.tag_name,
            release.name.as_deref().unwrap_or("(none)"),
            release.draft,
            release.prerelease
        );
    }

    Ok(())
}

```
