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

## Structures, Classes, Unions, and Interfaces

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

## Variables

Variables are generally pretty simple to represent. They have a name, which
represent a chunck of memory or a register that contain some kind of value.
The kind of values that the variable can contain, and restrictions about
whether or not it can be modified, are described by the type of the variable.

What distinguishes variables is where the value is stored, and its scope.

The scope of a variable determines where the variable can be used, and is,
usually, related to where the variable is declared. DWARF documents where
a variable is declared in the source file using a (file, line, column) triplet.

DWARF splits variables into three categories: constants, function parameters,
and variables. Most variables have a location attriubte that describes where
the variable is stored. In simple cases, a variable is stored in memory and
has a fixed address.

Many variables, such as those declared within a C function, are dynamically
allocated, and locating them requires some (simple) computation. For example,
a local variable may be allocated on the stack, and locating it may be as
simple as adding an offset to the a frame pointer.

## Location Expressions

DWARF provides a simple scheme to describe how to locate the data represented
by a variable. A DWARF location expressions contains a sequence of operations
that tell a debugger how to locate the data.

DWARF location expressions can contain a sequence of operators and values
that are evaluated by a simple stack machine. This can be an artitrarily
complex computation, with a wide range of operations, tests, branches, calls
to evaluate other location expressions, and accesses to the processors memory
or registers.

# Describing Code

### Functions and Subprograms

DWARF treats functions that returns values and subroutines that do not as
variations of the same thing. Both of these are described with a Subprogram
DIE. This DIE has a name, a source location triplet, and an attribute that
indicates whether the subprogram is external (i.e. lis it visible outside of
the current compilation block?).

A subprogram IDE has attribute that give the low and high memory addresses
that the subprogram occupies, if it is contiguous, or a list of memory ranges
if it is not contiguous. The low PC address is assumed to be the entry point
for the routine unless another is explicitly specified.

The value that a function returns is given by the type attribute. Subroutines
that do not return values do not have this attribute.

A function may define variables that may be local or global.

### Compilation Unit

Most interesting programs consist of more than a single file. Each source
file that makes up a program is compiled independently, and then linked
together with system libraries to make up the program. DWARF calls each
separately compiled source file a compilation unit.

A compilation unit DIE contains general information about the compilation,
including the directory and name of the source file, the programming language,
the etc.

The compilation unit DIE is the parent of all of the DIEs that describe the
compilation unit. Generally, the first DIEs will describe data types, followed
by global data, then the functions that make up the source file.

### Data Encoding

Conceptually, the DWARF data that describes a program is a tree. Each DIE
may have a sibling, and several DIEs that it contains. Each of the DIEs has
a type (its TAG), and a number of attributes.

Each attribute is represented by an attribute type and a value. Unfortunately
this is not a very dense encoding. Without compression, this would become
unwieldy. In order to save space, several techniques are used.

The first is to flatten the tree by saving it in prefix order. Each type
of DIE is defined to either have children or not. If the DIE cannot have
any children, the the next DIE is its sibling. If the DIE can have children,
then the next DIE is its first child. The remaining children are represented
as siblings of the first child. This means that links to the sibling or child
DIEs can be eliminated.

A second scheme to compress the data is to use abbreviations. Although DWARF
allows for great flexibility in which DIEs and attributes it may generate,
most compilers only generate a limited set of DIEs, all of which have the
same set of attributes.

# Other DWARF Data

### Line Number Table

The DWARF line table contains the mapping between the source lines for the
executable part of a program, and the memory that contains the code that
corresponds to that source.

In the simplest form, this can be looked at as a matrix with one column
containing the memory addresses and another column containing the source
triple (file, line, column).

If you want to set a breakpoint at a particular line, the table gives you the
memory address at which to store the breakpoint instruction. Conversely, if
your program has a fault, you can look for the source line that is closest
to the memory address.

To save space, DWARF keeps this from growing too large by encoding it as a
sequence of instructions called a line number program. These are interpreted
by a simple finite state machine to recreate the complete line number table.

