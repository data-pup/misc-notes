### Parsing Object Files Notes

The `object-and-gimli-notes.md` notes document contains a brief overview of
what the `gimli` and `object` crates are. This document contains some notes
taken while reading examples of how to use the `gimli` crate, so that I
can gain a better understanding of how to parse through debugging information.

The `gimli` crate itself contains some examples, but there is a fleshed out
example in the form of an `addr2line` clone implemented using this crate,
which can be found here: https://github.com/gimli-rs/addr2line

NOTE: This library assumes some previous familiarity with DWARF. The
`introduction-to-the-dwarf-debugging-format.md` file in this repository
contains some notes on this format for further information.

### Using a Fallible Iterator

The `fallible_iterator::FallibleIterator` object is used when we step through
the debugging information. This is used for a couple of reasons. Most
importantly, the `Iterator` trait in the standard library does not handle
situations where the `next` method can fail, i.e. returns a `Result` object.

`gimli` offers lazy iterators, so we might not encounter an error until we
have progressed into a file. Because iterating through the debugging
information can fail, the fallible iterator trait should be used to that its
methods are in scope.

### `dwarfdump` Clone Implementation Notes

First, we'll look at the `dwarfdump` example in the `gimli` crate, to see what
steps are involved with dumping DWARF information in a object file.

We create a reference to a file on the filesystem, using `fs::File::open`, and
then create a memory map of the file using `memmap::Mmap::map`. In our case
for `twiggy`, we already have a `[u8]` set up, so we won't need to worry
about this setup.

`object::File::parse` parses the ELF binary. Then, we can determine the
endian-ness of the data using this excerpt, which will be used by `gimli`.

```rust
let endian = if file.is_little_endian() {
  gimli::RunTimeEndian::Little
} else {
  gimli::RunTimeEndian::Big
};
```

The prototype of the `dump_file` function is also useful to check out. It looks
like this. Note the trait bounds:

```rust
fn dump_file<Endian>(file: &object::File, endian: Endian, flags: &Flags) -> Result<()>
where
    Endian: gimli::Endianity,
{
  // ...
}
```

This method contains a lot of logic, but the most pertinent parts that will
also relate to what we are doing is in the process of loading sections.

```rust
fn load_section<'input, 'file, S, Endian>(
  file: &'file object::File<'input>,
  endian: Endian,
) -> S
where
  S: gimli::Section<gimli::EndianBuf<'input, Endian>>,
  Endian: gimli::Endianity,
  'file: 'input,
{
  let data = file.get_section(S::section_name()).unwrap_or(&[]);
  S::from(gimli::EndianBuf::new(data, endian))
}
```

This is called a number of times in sequence to load the different sections
of debugging information.

```rust
let eh_frame = &load_section(file, endian);
let debug_abbrev = &load_section(file, endian);
let debug_aranges = &load_section(file, endian);
let debug_info = &load_section(file, endian);
let debug_line = &load_section(file, endian);
let debug_loc = &load_section(file, endian);
let debug_pubnames = &load_section(file, endian);
let debug_pubtypes = &load_section(file, endian);
let debug_ranges = &load_section(file, endian);
let debug_str = &load_section(file, endian);
let debug_types = &load_section(file, endian);
```

### `addr2line` Implementation Notes

Next, let's look into how the `addr2line` project generally makes use of these
libraries, before we start trying to use it for `twiggy`.

Todo...

