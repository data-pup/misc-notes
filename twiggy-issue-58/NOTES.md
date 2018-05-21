
### Todo

- [x] Set up test cases for the regex functionality
  - [ ] Set up further test cases (invalid regex, complete wildcard, etc.)
- [ ] Add the `regex` crate as a dependency to the `Cargo.toml` file.
- [ ] Take notes on changes that will be required, post in issue.

NOTE: Unrelated, but I should open up an issue regarding the fact that the
      Json output for the `twiggy paths` does not implement the descending
      feature.

### Implememtation Notes

Some points of consideration when adding this feature will involve the fact
that the regex patterns might not neccessarily be disjoint. Further, this
struct that is returned by the `paths` function will probably need to change.

For reference, it is currently defined like this:

```rust
struct Paths {
  items: Vec<ir::Id>,
  opts: opt::Paths,
}
```

I think the most sensible way about this will be to change this `Vec<ir::Id>`
object to a different kind of collection. At first glance, something like
`Vec<BTreeSet<ir::Id>>` (a vector of btreesets of Id's) might be a nice type.
When emitting the output to text/json/etc., we could then summarize each
group of items (cummulative size, number of items, etc.).

NOTE: It might also be nice to try and print groups of items differently
      than a single item. i.e. if a single item is matched by an argument,
      we might print different information than if an argument is a regex
      that matches a group of items in the binary.

NOTE: How should we go about error handling, in the case of an invalid regex?
      This might also be worth considering for the case when a item name or
      regex is given that does not actually match any items in the binary.

### Initial Steps (Adding Tests)

The first step was adding a new test case. Handling some simple cases will be
my first concern before I start worrying about some of the specific details
such as invalid regex patterns, missing matches, and so forth.

It does indeed fail! Yay!

On further thought, I also realized something useful about this:
rather than changing the `items` member as mentioned above, I can set up a
collection of regex patterns, and simply add any item that matches at least
one of the patterns in our arguments.

This seems convenient because the emitting code will not need to change very
much, and most of the changes will occur upstream in the `paths` function.

I also added some new test cases to demonstrate matching items that are
exports, i.e. `export "foo"`. This includes a case using the default emission,
as well as emitting the retaining paths in descending order.

### Error Handling Notes

Looking into things a little more, I noticed that the functions in `analyze.rs`
are actually returning `Result` objects! This is good, and means that we should
be able to identify the situation where an invalid regex pattern is given.

For reference, the functions look generally like this in terms of signature:

```rust
pub fn function(items: &ir::Items, opts: &opt::Garbage)
  -> Result<Box<traits::Emit>, traits::Error>
{
  // ...
}
```

NOTE: These traits, including the `Error` item, are define in
`traits/traits.rs` inside of the `twiggy_traits` crate. We might or might
not need to add a new error type to the `ErrorInner` enum, depending on
what the `Regex::from_str()` function returns in the event of an error.
This will need to be looked into.

NOTE: Will it be possible to use the existing `test!` macro to see if something
explicitly fails? This will also need to be looked into.

### Adding the `regex` crate dependency

Is there a specific version already being used in any of the crates in the
workspace? If so, use the same version for the sake of consistency. Otherwise,
we will need to add this (most likely specifically to `twiggy_analyze`).

NOTE: This will be fleshed out in more detail in a different document.

