# Cretonne Issue 342

### Overview

Cretonne currently uses a Python-based DSL, which allows target ISAs to be
described in a concise manner. This is a common practice for compilers, other
examples include LLVM's `tablegen`, or gcc's machine description language.

For various reasons, it would be nice if this DSL could eventually be
implemented using Rust. While in the future it would be nice to use procedural
macros, these are not yet stable. So, an intermediate step would involve using
a `build.rs` file.

To start setting up some of this infrastructure, we can start by rewriting some
of the simpler components. `lib/codegen/meta/gen_types.py` would be an
excellent candidate for this.

When the project is built, the output file is:
`target/debug/build/cretonne-codegen-*/out/types.rs`

### Task 01

The first step, as mentioned above, is rewriting the `gen_types.py` file.
Mentorship instructions provided on this issue are below:

```
Thinking about this more, I think using Rust, with a build.rs approach, is the
best one right now.

So as I mentioned above, I think gen_types.py is a good initial goal. And
looking at it more closely, gen_types.py depends on srcgen.py. And, srcgen.py
is a very simple file that should be very straightforward.

So the very first step here is to make a Rust version of srcgen.py. It's a
simple file that just makes it easy to produce Rust source code, managing
things like indent levels.
```

