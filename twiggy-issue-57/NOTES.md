#Issue 57 Notes

## Overview

If no argument is given to the `twiggy paths` command, then all of the paths
should be displayed.

In order to accomplish this, we will need to identify:
*  Where is the path set for the paths command?
*  How should we set this (meta-root) so that all paths are displayed?

The `Paths` struct is created by the `paths` function in `analyze.rs`,
and is defined like this:

```
struct Paths {
  items: Vec<ir::Id>,
  opts: opt::Paths,
}
```

`items` is a list of item Id's, the paths of which are to be printed.

note: we might be able to reuse some of the same logic from the dominators
subtree work done previously. I.e., we can use the `get_item_by_id` function
added to `ir::Items` to grab the items to print, otherwise using the meta
root Id value.

## Questions

If the default value of `-r` is 10, how should we sort the output? Sorted
by shallow size (descending)? Number of calls?

What if the same function calls the same thing twice? etc.

What if the argument is not a function?

Right now, it seems like the path displays the path from the bottom, up to
the top. Displaying -all- items might be tricky here?

## Misc

descending/ascending flag?
flag to see deepest/shallowest call paths?
finding all leaf nodes?

## To Do

- [ ]  Update the existing test fixtures, test command with/without arguments.
- [ ]  Get a list of all the functions (default behavior)
- [ ]  Write some test fixtures by hand, (yay more web assembly)
       - [ ] Write a small binary that includes two functions, `calledOnce`
             and `calledTwice`. These should have 1 and 2 paths respectively.

## How does the paths functions work?

Before I started changing things, it is worth documenting how the `paths`
function works. This can be found in `analyze.rs`.

### Working Notes

I wrote a test fixture, and need to verify it, but this should provide a wasm
module that involves some various call paths to traverse.

