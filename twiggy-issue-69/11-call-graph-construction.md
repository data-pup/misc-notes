# Call Graph Construction Notes

### Context

At this point in my progress in this project, I could successfully collect
different objects from a simple ELF binary. The size calculation for these
objects is not yet in place, but I am in a decent position to start working
on the construction of the call graph.

This document will contain some of the notes on this work.

### Adding `parse_edges` Scaffolding

First, I should add some of the same scaffolding that in place to parse through
the items in a binary, progressing from the file, to the compilation units,
to the individual debugging information entries.

### Collecting Subroutines

I should collect the different items that represent subroutines, and find
their call sites. Note that subroutines can be implicitly inlined. For more
detailed information about this, refer to Chapter 3.3 in the DWARF v5 spec.

