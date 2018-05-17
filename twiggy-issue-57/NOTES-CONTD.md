#Issue 57 Notes (Contd.)

## Overview

This document contains some further work on the issue of printing all call
paths if no argument is given.

Current issues that I am dealing with:
*  Does descending the call path make sense for the default case?
*  Should I keep `type[x]` items out of this output? (I think so)
*  If the above is true, how should I identify these items?
*  Should output be sorted if function names are given, or should these be printed in the order given?
*  What should I call the directional flag? `-a` for ascending seems terrible,
   `-d` is also not great, and long options only seem like a bad solution.

__Current Progress:__

As of right now, I can successfully print the call path downwards starting
from the meta-root, but type information is showing up. I need to sort these
items as well.

I added an `is_debug` method to the `Item` struct so that the function names
section is not included, since that is a successor to the meta-root.

I have added a new test fixture, and some basic test cases to mirror the
existing ones on this binary, and am working on a new `expectations` file that
will include the expected default output when no argument is given to the
`paths` subcommand.

__Review Notes:__

Note that this isn't neccessarily a 'call graph', but rather a
dependency/retaining graph (words are hard!), so the information in this tree
can include type information, etc.

Type items should be included in the output.

Sort items to print by their shallow size. This is mostly relevant to the
case wherein no specific arguments are given to the sub-command.

```
But we want (and right now have) vertices for anything taking up space in the
binary. This includes data segments and custom sections, as well as each type
entry. Instead of having edges only where one function calls another function,
we also have edges from X to Y whenever X requires that Y also be present in
the binary.
```

__Next Steps:__

[x] - Remove the `successor` work, this can be done using `neighbors` instead.
[x] - Sort items by size if no parameter is given.
[x] - Replace the arrow shown when the graph is being descended, so it's not confusing.
[x] - Remove the `calledThrice...` function, recompile the wasm module.
