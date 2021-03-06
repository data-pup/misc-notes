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

UDPATE: Test fixture builds correctly, and seems to be correct, after
(cursorily) examining the output of the paths command and the top command.
However, it will be worth revisiting whether or not the number of times that
a function is called from a specific place actually has any meaning in this
case. It -may- be worth examining the -number of places- from which a function
is called.

The next step I believe is to build some test cases that follow the same
pattern as the existing test cases. From there, I will be able to look into
how to handle the case of no parameter being given, i.e. printing all of
the call paths. I also think it may be worth checking into a flag or option
that would print the call chains from the top down.

- [x] Add test expectations for the call paths for specific functions in my new fixture.

---

At this point, I have essentially a copy of the same tests that are run on the
`wee_alloc` file, which is a nice start on things. I still think that a
descending flag of some sort would be nice, but before I go around adding
options, I decided I should (a) add some -failing- tests to show what I want
to expect to be generated, and (b) read more about the existing options for
this subcommand.

__Why a descending flag?__

This (iiuc) is distinct from the dominators command in a specific way that
might be worth noting. A dominators tree consists of nodes wherein a node `a`
is a dominator of `b` if _all_ paths to `b` go through `a`. This can look
similar to traversing the call paths from the top down, but it is worth noting
that this is distinct in the sense that if a function is called from two
places, it is dominated by neither.

(am i trapped in graph theory hell?)

---

Coming back to this, I think a good starting point would be to identify all
of the public functions that are exported from a module.

(days of reading source pass)

AHHHH what if I compute successors too! `ack` seems to think the predecessors
tree is only used for paths, so I can force computation of a similar tree
when we want to go from the top down. Otherwise, that can stay as `None`!!!

