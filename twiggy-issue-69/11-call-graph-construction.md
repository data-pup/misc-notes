# Call Graph Construction Notes

### Context

At this point in my progress in this project, I could successfully collect
different objects from a simple ELF binary. The size calculation for these
objects is not yet in place, but I am in a decent position to start working
on the construction of the call graph.

This document will contain some of the notes on this work.

*  `parse_edges` trait method scaffolding
*  Collecting subroutines
*  Calculating size of subroutines

### Adding `parse_edges` Scaffolding

First, I should add some of the same scaffolding that in place to parse through
the items in a binary, progressing from the file, to the compilation units,
to the individual debugging information entries.

### Collecting Subroutines

I should collect the different items that represent subroutines, and find
their call sites. Note that subroutines can be implicitly inlined. For more
detailed information about this, refer to Chapter 3.3 in the DWARF v5 spec.

---

## Chapter 3.3 Notes

Note: A subroutine entry may contain a `DW_AT_main_subprogram` flag to denote
that it is the main function.

### Call Site Attributes

Chapter 3.3.1.3 covers call site related attributes. This excerpt covers some
of the information regarding these attributes.

> A subroutine entry may have `DW_AT_call_all_tail_calls`, `DW_AT_call_all_calls`
> and/or `DW_AT_call_all_source_calls` attributes, each of which is a flag. These
> flags indicate the completeness of the call site information provided by call
> site entries (see Section 3.4.1 on page 89) within the subprogram.)

### Subroutine Size

Regarding the size of a subroutine, these are similar to compilation units.

> A subroutine entry may have either a `DW_AT_low_pc` and `DW_AT_high_pc`
> pair of attributes or a `DW_AT_ranges` attribute whose values encode the
> contiguous or non-contiguous address ranges

