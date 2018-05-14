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

