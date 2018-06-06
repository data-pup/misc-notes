# Creating IR Items Notes

I spent the last few days reading more about DWARF and refactoring some of my
previous work so that individual items implement the `Parse` trait. At this
point, I need to answer some specific questions to progress further.

*  What tags should correspond with what values of `ItemKind` ?
*  How should I instantiate these items, what data is required etc.?

Once this is complete, I can progress to taking care of the call graph stuff.

## `ir::Item` Implementation Review

Let's start by reviewing the current implementation of `Item`, and go from
there!

```rust
/// An item in the binary.
#[derive(Clone, Debug, PartialEq, Eq)]
pub struct Item {
  id: Id,
  name: String,
  size: u32,
  kind: ItemKind,
}
```

These are created using this constructor:

```rust
pub fn new<S, K>(id: Id, name: S, size: u32, kind: K) -> Item
where
  S: Into<String>,
  K: Into<ItemKind>,
{
  // ...
}
```

`name` and `size` are clear enough, the other two fields are structs that
are defined within the `ir` module.

Item's are identified using a unique identifier, containing the section index,
and a value specifying the item iteself. This is what an `Id` value is:

```rust
/// An item's unique identifier.
/// (section index, item within that section index)
pub struct Id(u32, u32);
```

At this point, I have the `Id` stuff basically taken care of, `name` I might
need to revisit for things that are not explicitly named, but for now I will
assume that everything has a `DW_AT_name` attribute.

`size` will require working with one of the following, depending on the item:
*  `DW_AT_location`
*  `DW_AT_low_pc`
*  `DW_AT_low_pc` and `DW_AT_high_pc`
*  `DW_AT_ranges`

We will come back to that, but first let's think about the `ItemKind`.

### `ItemKind`

This is an `enum` that looks like this:

```rust
pub enum ItemKind {
  /// Executable code. Function bodies.
  Code(Code),

  /// Data inside the binary that may or may not end up loaded into memory
  /// with the executable code.
  Data(Data),

  /// Debugging symbols and information, such as a DWARF section.
  Debug(DebugInfo),

  /// Miscellaneous item. Perhaps metadata. Perhaps something else.
  Misc(Misc),
}
```

So, we should understand what the contained `Code`, `Data,`, `DebugInfo`, and
`Misc` types require/are.

__Code__

These represent executable code, i.e. function bodies.

These are created with a call to `new` providing the demangled name of the
code in the form of an `Option<String>`.

__Data__

These represent data that may end up being loaded into memory, i.e. types.
These are created by providing an `Option<String>` containing the type.

__Debug__

These represent debugging information and symbols, and are not given any
information when constructed. For now, I will step past this.

__Misc__

This is a catch-all for other things in the binary. These are also not given
any info as far the `ItemKind` member goes.

### Miscellaneous Style Guide Note

At this point there was enough complexity that I believed that it would be
worth annotating comments with references to the DWARF standard specification.
How wide should lines consisting of only comments be?

```
Source lines which are entirely a comment should be limited to 80 characters in
length (including comment sigils, but excluding indentation) or the maximum
width of the line (including comment sigils and indentation), whichever is
smaller.
```

