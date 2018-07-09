# Cretonne Progress Notes

At this point in my project, I had successfully completed the initial process
of generating a file in the target directory. There are some problems to sort
out, and not all of the types are in place as of yet, but I do see a file in
the output directory nonetheless!

The contents of the `new_types.rs` file look like this:

```
0/// Hello
0pub const IR::TYPES::FLAGTYPE: Type = Type(0x0);
0/// Hello
0pub const IR::TYPES::FLAGTYPE: Type = Type(0x0);
```

On the bright side, this is getting close to the general structure of output
that we need. On the downside, there are currently a few issues with this.
We will need to:

*  Remove the leading 0's from each line.
   (Find out why are these being initialized in the first place)
*  Figure out why multi-line doc comments do not seem to work.
*  Fix the naming problem.
*  Fix the numbering system.
*  Remove placeholder documentation comments.
*  Fix iterator creation for the special types.
*  Once special types seem correct, flesh out the rest of the types.

For reference, the equivalent flags in the `types.rs` file look like this:

```
/// CPU flags representing the result of an integer comparison. These flags
/// can be tested with an :type:`intcc` condition code.
pub const IFLAGS: Type = Type(0x1);

/// CPU flags representing the result of a floating point comparison. These
/// flags can be tested with a :type:`floatcc` condition code.
pub const FFLAGS: Type = Type(0x2);
```

---

### Removing Leading 0's

As shown above, there are some errant digits shown at the beginning of each
line. That seems like a good place to start.

Because this seems to be appearing at the beginning of lines that are both
documentation comments, as well as the generated Rust code itself, I think
this is something related to the file updating, rather than specific methods
such as the the `.doc()` method.

After investigating further, I found by adding some println statements to
the `update_file` method of the `srcgen::Formatter` class that the 0's
were already prepended to the lines. This was strange, but it seemed that
the `line()` method would be the next place to look. Perhaps this is being
caused by a problem with the indentation logic?

NOTE: Because these are invoked by the build script, the output is found
in a special file in the `target` directory, alongside where cargo flags are
set. This isn't especially convenient, but there isn't really a way around
that.

