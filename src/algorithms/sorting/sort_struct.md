## Sort a Vector of Structs

[![std-badge]][std] [![cat-science-badge]][cat-science]

Sorts a Vector of Person structs with properties `name` and `age` by its natural
order (By name and age). In order to make Person sortable you need four traits [`Eq`],
[`PartialEq`], [`Ord`] and [`PartialOrd`]. These traits can be simply derived.
You can also provide a custom comparator function using a [`vec:sort_by`] method and sort only by age.

```rust,edition2024
#[derive(Debug, Eq, Ord, PartialEq, PartialOrd)]
struct Person {
    name: String,
    age: u32
}

impl Person {
    pub fn new(name: &str, age: u32) -> Self {
        Person {
            name: name.to_string(),
            age
        }
    }
}

fn main() {
    let mut people = vec![
        Person::new("Zoe", 25),
        Person::new("Al", 60),
        Person::new("John", 1),
    ];

    // Sort people by derived natural order (Name and age)
    people.sort();

    assert_eq!(
        people,
        vec![
            Person::new("Al", 60),
            Person::new("John", 1),
            Person::new("Zoe", 25),
        ]);

    // Sort people by age
    people.sort_by(|a, b| b.age.cmp(&a.age));

    assert_eq!(
        people,
        vec![
            Person::new("Al", 60),
            Person::new("Zoe", 25),
            Person::new("John", 1),
        ]);

}

```

[`Eq`]: https://doc.rust-lang.org/std/cmp/trait.Eq.html 
[`PartialEq`]: https://doc.rust-lang.org/std/cmp/trait.PartialEq.html
[`Ord`]: https://doc.rust-lang.org/std/cmp/trait.Ord.html
[`PartialOrd`]: https://doc.rust-lang.org/std/cmp/trait.PartialOrd.html
[`vec:sort_by`]: https://doc.rust-lang.org/std/vec/struct.Vec.html#method.sort_by
