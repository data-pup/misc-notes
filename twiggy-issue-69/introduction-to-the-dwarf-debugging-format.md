# Notes on 'Introduction to the DWARF Debugging Format'

### Overview

This document contains notes Michael Eager's introduction to DWARF. The PDF
can be found [here](http://www.dwarfstd.org/doc/Debugging%20using%20DWARF.pdf).

---

# Introduction

Unfortunately, debugging programs is a thing that we as programmers are always
going to need to do. While low-level debuggers exist to step through the
machine code instructions, viewing registers and memory in binary, this is
not always an efficient or especially comprehensible manner to debug programs.

Often, it is much more desirable to use a source-level debugger that steps
through the lines of source code rather than the compiled instructions,
allowing us to set breakpoints, print variables, and call functions.

This introduces the problem of coordinating a debugger, and the compiler, so
that we can keep track of the relationship between the original source code,
and the machine code in the resulting binary.

### Translating from Source to Executable

Compilers are complex, and a field of study in and of themselves, but what is
relevant to us in this context is that compilers perform lots of optimizations
on a program while generating a binary. Things like inlining, dead-code
elimination, loop-invariant code motion, peephole optimizations, all result
in a compact and performant series of instructions that may be difficult
to understand, and may bear little resemblance to the original source code.
This makes the process of relating low-level instructions to source code
difficult for programmers, and for the compiler's optimizer.

The second challenge is finding a method to describe the executable and its
relationship to the original source with enough detail for a debugger to
understand, without introducing size or runtime issues.

### The Debugging Process

There are some common operations that a programmer would their debugger to
perform. The most common would be setting a breakpoint based on a line number
or function name. When the breakpoint is hit, we might like to display the
values in different local and global variables, arguments to a function, and
the call stack, for example.

Other important operations include stepping through a program line by line,
bypassing a function and substituting the result with a given value.
The challenge that DWARF aims to solve is making these types of operations
possible, and easy.

### Debugging Formats

For context it might be good to know some other debugging formats that we can
compare DWARF to. stabs, COFF, PE-COFF, OMF, IEEE-695, are all examples of
other similar formats.

### A Brief History of DWARF

DWARF originated with the C compiler and `sdb` debugger in Unix System V
Release 4 (SVR4), developed by Bell Labs in the mid-1980's. The main
shortcoming of this original format was that it was not very compact.

DWARF 2 was released in 1990, and attempted to address concerns related to
size, as well as adding support for C++. Reasons that this format did not
work out relate to a lack of concerted standardization, to summarize briefly.

DWARF 3 was designed by committee, and aimed to ensure that the DWARF standard
was readily available, and would avoid divergence caused by multiple sources.

# DWARF Overview

Most modern programming languages are block structured. This means that each
entity, such as a class or function, is contained within another entity. A
file in a C program might (and probably does) contain multiple data
definitions, variables, functions, and so forth.

This creates lexical scopes, where names are only known within the scope that
they are defined in. To find the definition for a particular symbol, you first
look in the current enclosing scope, then in successive enclosing scopes until
the symbol is found. This is related to why compilers represent a program
internally using a tree.

DWARF follows this model, and is also block structured. Each descriptive entity
in DWARF (except for the top-level entry, which describes the source file)
is contained within a parent entry, and may contain children entities.

The DWARF description of a program is a tree structure, similar to a compiler's
internal tree representation. Each node might represent types, variables, or
functions.

DWARF is designed to be extensible, so that this process can be used for as
many languages as possible, rather than being bound to a single language.
The same applies to the object file format -- while it is commonly associated
with ELF, DWARF is independent of a specific object file format.

DWARF also does not duplicate information that is already represented in the
object file, such as identifying processor architecture, or big/little endian
format.

# Debugging Information Entry (DIE)

### Tags and Attributes

The basic descriptive entity in DWARF is the debugging information entry (DIE).
A DIE has a tag, which specifies what the DIE describes, and a list of
attributes which further describe the entity.

As mentioned before, each DIE except for the top-most entity is contained/owned
by a parent entity, and may have siblings or children DIEs.

Attributes may contain a variety of values: constants (like a function name),
variables (like the start address for a function), or references to another
DIE (like the type for a function's return value).

### Types of DIEs

DIEs can be divided into two groups. One being those that describe data, and
those that describe functions and other executable code.

# Describing Data and Types

DWARF provides `base types` that refer to the primary data types built directly
on the hardware. Other more complex data types are abstrations build using
collections or compositions of these base types.

### Base Types

Every programming languages defines some basic scalar data types. C and Java
define `int` and `double` for example. This often involves a large amount of
nuance regarding how different languages implement different data types, so
DWARF base types provide the lowest level mapping between simple data types
and how they are implemented on the target machine's hardware.

Here is an example of a DIE describing an `int` on a 16-bit processor.

```
DW_TAG_base_type
  DW_AT_name = int
  DW_AT_byte_size = 2
  DW_AT_encoding = signed
```

### Type Composition

A named variable is described by a DIE which has a variety of attributes.
One attribute will be a reference to a type definition.

An example of a simple DIE describing an integer variable is shown below. For
now, skipping some of the information that is usually contained in a DIE
describing a variable.

```
<1>: DW_TAG_base_type
       DW_AT_name = int
       DW_AT_byte_size = 4
       DW_AT_encoding = signed

<2>: DW_TAG_variable
       DW_AT_name = x
       DW_AT_type = <1>
```

The base type for `int` (this is the block at `<1>`), described it as a signed
integer occupying four bytes. The `DW_TAG_variable` DIE for `x` provides its
name and type attribute.

NOTE: The DIES are labeled sequenctially in this example,. In actual DWARF
data, a reference to a DIE is the offset from the start of the compilation
unit where the DIE can be found.

# Structures, Classes, Unions, and Interfaces

Most languages allow a programmer to group data together into structures. Each
of the components of the structure generally has a unique name, and may have
a different type.

While each language has its own terminology, i.e. C++ calls these components
'members' while Pascal calls them 'fields', the underlying organization can
be described in DWARF. DWARF uses the C/C++ terminology and has DIEs which
describe 'struct', 'union', 'class', and 'interface'.

These DIEs generally looks similar to variables, but may include some extra
information. For example, the 'accessibility' attribute will describe whether
the member is public, private, or protected.

