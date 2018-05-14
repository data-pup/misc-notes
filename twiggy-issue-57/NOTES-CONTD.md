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

