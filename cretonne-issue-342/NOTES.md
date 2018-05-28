# Cretonne Issue 342 Implementation Notes

First, implement a Rust version of `srcgen.py`, which the `gen_types.py` file
depends on. Once this is complete, implement a Rust version of the
`gen_types.py` file.

## `srcgen` Re-implementation Notes

As mentioned in the `README`, the first step towards getting this issue solved
will entail rewriting the `srcgen` file in Rust. This file is where the bulk
of the work will be, as this file contains more logic than the `gen_types`
file itself.

### `srcgen` Overview

The first step was reading through the source of this file and gaining an
understanding of the file. In general, this contains some various classes
used for emitting/generating code, including things like tracking indentation
level, emitting comments, etc.

*  class `Formatter`
  * class `_IndentedScope`
  *  `__init__(self)`
  *  `indent_push(self)`
  *  `indent_pop(self)`
  *  `line(self, s=None)`
  *  `outdented_line(self, s)`
  *  `writelines(self, f=None)`
  *  `update_file(self, filename, directory)`
  *  `indented(self, before=None, after=None)`
  *  `format(self, fmt, *args)`
  *  `multi_line(self, s)`
  *  `comment(self, s)`
  *  `doc_comment(self, s)`
  *  `match(self, m)`
*  class `Match`
  *  `arm(self, name, fields, body)`

### `build.rs` Overview

The next step is understanding how the `build.rs` file works, so I can replace
the existing Python implementation of this file with a new Rust version,
after figuring out where the file should go, and make sure that all of the
plumbing is working correctly.

*  `main` - This program expects a specific environment:
    *  `OUT_DIR` - Directory where generated files should be placed.
    *  `TARGET` - Target triple provided by Cargo.
    *  `CRETONNE_TARGETS` (Optional) -  A setting for conditional compilation
        of isa targets. Possible values can be "native" or known isa targets.
*  `enum ISA` - This represents known ISA targets.

`build.rs::main` is in effect checking the environment and running the
following shell command from within `lib/codegen`:

```sh
python -B build.py --out-dir $OUT_DIR
```

NOTE: The `-B` flag is used to disable `.pyc` files, which cause problems
for vendoring scripts.

### Replacing `srcgen`

`lib/codegen/meta/srcgen.py` -> `lib/codegen/src/srcgen.rs`

__Day 00 Work Notes__

My first steps for this work were to add some initial scaffolding with drafts
of each class/method in the original file. Some of the methods seemed like
they might require some more nuance, or complex types that I wanted to
wait on, so I used the `unimplemented!` macro for these.

I am still a little hazy on how the plumbing for this is going to work, and
also noticed that one of the methods in the original file are using a keyword
that is reserved in Rust. So, any places that this method is referenced will
need to be edited. I am also a little concerned about how this code can
be effectively tested. That will be my first point of order to take care of
tomorrow.

