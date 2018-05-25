# Twiggy Issue 69

### Overview

The goal of this issue is to add compatibility with ELF binaries to `twiggy`.

This directory will contain some various notes and links gathered during this
process.

`goblin` is a crate that will help us parse IR nodes.

The `object` crate will be useful as well, this might be useful for handling
both ELF and Mach-O at the same time!

### Initial Advice (via @fitzgen)

We will need to consume the DWARF info in a binary, (see the `object` crate).

To start, we will only worry about constructing the call graph. We can do
this using `DW_AT_call_site`, as well as using the information about
inlined routines.

This information will all be found in the `.debug_info` section, which can
be interfaced with using the `gimli` crate via `gimli::DebugInfo`.

