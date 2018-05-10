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

```rust
items.compute_dominator_tree();
items.compute_retained_sizes();
```

Here is the struct returned by the `dominators` function.

```rust
struct DominatorTree {
  tree: BTreeMap<ir::Id, Vec<ir::Id>>,
  opts: opt::Dominators,
}
```

### Computing a Subtree

At this point we have two options as far as considering how we would like to
go about computing and printing the requested subtree. The other options,
such as depth/rows, are not used in the `dominators` function, but rather
when emitting text or json.

The dominator tree is computed by the `petgraph` crate, by the
`petgraph::algo::simple_fast` function. We might like to use the
subtree option when computing the dominator tree, because this `simple_fast`
function accepts a parameter containing the root Id.

On further investigation, this could cause all sorts of strange adjustments,
and it would probably be much easier to instead focus on setting a start
for the emitting logic.

