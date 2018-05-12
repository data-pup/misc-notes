# Issue 57 Notes

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

## To Do

- [ ]  Update the existing test fixtures, test command with/without arguments.
- [ ]  Write some test fixtures by hand, (yay more web assembly)
       - [ ] Write a small binary that includes two functions, `calledOnce`
             and `calledTwice`. These should have 1 and 2 paths respectively.

