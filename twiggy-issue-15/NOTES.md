# Issue 15 Notes

## Overview

Add an argument to the `dominators` sub-command so that a specifif sub-tree
can be viewed, rather than the entire dominator tree.

## TODO:

- [x]  Create expected outputs for test cases
- [x]  Add `test!` macro calls including subtree name
- [ ]  Add a test case that limits the depth/rows in the subtree. (Neccessary?)
- [x]  Add argument to `dominators` sub-command
- [ ]  Add logic to return only the subtree.

### Command Options

Added a `func_name` member, but unsure about the typing of this. It could
alternatively be represented as a (u32, u32), but that brings up the issue
of (u32, u32) implementing the `FromStr` trait. Alternatively, we could
import `ir::Id` and implement that trait? Otherwise, we can step through
the dominator tree and find the item whose name matches the `func_name`
option. If that value is empty, then we should start at the meta root.

### Computing the dominator tree

These lines in the `dominators` function in `analyze/analyze.rs` are
responsible for computing the dominator tree, and the retained sizes of items.
The `--function` option should not play a role in that, but should find the
subtree before we create the output object, and box it up in a trait object
to return.

```
items.compute_dominator_tree();
items.compute_retained_sizes();
```

After that is done, we should check if a subtree has been requested, then
traverse the dominator tree and find an item with a matching name if so.

