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

*  [x] Remove the leading 0's from each line.
   (Find out why are these being initialized in the first place)
*  [x] Figure out why multi-line doc comments do not seem to work.
*  [ ] Fix the naming problem.
*  [ ] Fix the numbering system.
*  [ ] Remove placeholder documentation comments.
*  [ ] Fix iterator creation for the special types.
*  [ ] Once special types seem correct, flesh out the rest of the types.

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

So at this point I decided to trace through the logic of the `gen_types` file.
*  `generate`: the public function called by the codegen crate's `build.rs`
*  `emit_types`: iterates through each type and calls `emit_type`
*  `emit_type`: adds the types' doc comments and the type declaration itself.

Aside from some miscellaneous things like creating a `Formatter` constructor
and the iterator logic, the `line` and `doc_comment` methods are the primary
ways that `gen_types` interacts with the `srcgen` file.

Before I delved into that, I spent some time cleaning up the project and
making edits to surpress various compiler warnings. Once this was complete,
I decided I had a good chance to build some new test cases that would explain
why the 0 was being prepended. It almost certainly has something to do with
the scheme for generating indentation, and its base case.

**Building a Failing Test**

The first step in a situation like this would be to design a test case that
fails. This will help us isolate the problem, ensure that we have actually
fixed the issue, and that we don't reintroduce it with other changes later on.

The test I added to `srcgen.rs` looks liked this:

```rust
#[test]
  fn fmt_can_add_type_to_lines() {
  let mut fmt = Formatter::new();
  fmt.line(&format!(
    "pub const {}: Type = Type({:#x});",
    "example",
    0,
  ));
  let expected_lines = vec![
    "pub const example: Type = Type(0x0);\n",
  ];
  assert_eq!(fmt.lines, expected_lines);
}
```

**Fixing the Problem**

The fix related to how I was generating the indent string.

```
fn get_indent(&self) -> String {
-        format!("{}{:-1$}", " ", self.indent * SHIFTWIDTH)
+        if self.indent == 0 {
+            String::new()
+        } else {
+            format!("{:-1$}", " ", self.indent * SHIFTWIDTH)
+        }
}
```

### Fixing multiple line comments.

For some reason, the second line of my comments does not seem to work
correctly. In the example above, the documentation comments should be two lines
that say "Hello" and "Documentation" respectively. Instead, the line looks like
this: (After fixing the leading 0 problem, as shown above.)

```rust
/// Hello
pub const IR::TYPES::FLAGTYPE: Type = Type(0x0);
```

So, what is happening here? To investigate, I added a new test, that looked
like this:

```rust
#[test]
fn fmt_can_add_doc_comments() {
  let mut fmt = Formatter::new();
  fmt.doc_comment("documentation\nis\ngood");
  let expected_lines = vec!["/// documentation\n", "/// is\n", "/// good\n"];
  assert_eq!(fmt.lines, expected_lines);
}
```

This bug related to the fact that the `indent` variable in the multiline
parsing function was an option. So, in certain cases, this was not evaluating
the rest of the lines, if there was no indentation.

