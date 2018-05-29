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

__Day 01 Work Notes__

Some details that I want to figure out today regarding this work:

*  How is the `srcgen::Formatter` referenced?
*  Adding a `gen_types.rs` file to the `codegen` crate (in `ir/types.rs`)
*  What to do about the `def format(self, fmt, *args)` method in `srcgen::Formatter`
*  What should the type of dictionary key/values be for the `srcgen::Match.arms` member?
*  Implementing the `match` method

The `Formatter` class seems to only be referenced in the `gen_types` file
as far as I can tell at the moment, but I also have not delved especially
deep looking for that.

The type of the `arms` member is notated as: `OrderedDict[Tuple[Tuple[str, ...], str], OrderedDict[str, None]]`.
This is a little opaque at first, but should generally correspond to a
`BTreeMap` in Rust, if I understand correctly. To break down that type a little
further, it can help to read it separated across a few lines:

```
OrderedDict[
  Tuple[Tuple[str, ...], str],    # Key   - A 2-elem tuple, (n-tuple, str)
  OrderedDict[str, None]          # Value - An ordered dictionary with string keys.
]
```

There is also some helpful information that can be gleaned from the docstring
in the Python file's implementation of the `Formatter::match` method:

```
Python Example:

>>> f = Formatter()
>>> m = Match('x')
>>> m.arm('Orange', ['a', 'b'], 'some body')
>>> m.arm('Yellow', ['a', 'b'], 'some body')
>>> m.arm('Green', ['a', 'b'], 'different body')
>>> m.arm('Blue', ['x', 'y'], 'some body')
>>> f.match(m)
>>> f.writelines()
match x {
  Orange { a, b } |
  Yellow { a, b } => {
    some body
  }
  Green { a, b } => {
    different body
  }
  Blue { x, y } => {
    some body
  }
}
```

One thing that threw me off for a second here was the discrepancy between the
type of the ordered dictionary, and the arity of the `arm` method. Remember
that as mentioned above, the key of the dictionary is a tuple of two elements:
an n-tuple of strings (these are the members of the object we destructure),
and another string.

The tuple used as the key is comprised of `(fields, body)`, and then assigns
a key/value pair `name : None` to the dictionary stored as the corresponding
value for that key. It's a little complex to wrap one's head around at first,
but the idea is that two arms with the same name are equivalent, and are
automatically deduplicated using this scheme.

This might require some more work, but I eventually came up with a draft of
the struct that was typed like this:

```
struct Match {
  expr: String,
  arms: BTreeMap<(Vec<String>, String), HashSet<String>>,
}
```

I am not -totally- convinced of this `arms` type yet, but it seems like a
decent swing at the premise at least for now.

---

Next, I decided to see what happens if I added a `ir/types.rs` file to the
codegen crate under the `src` directory. Would this cause some sort of
conflict? At some point when I -am- done with this refactoring work, I am
going to have to replace the existing file anyhow, so there is definitely an
interesting question to answer here.

