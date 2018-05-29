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

