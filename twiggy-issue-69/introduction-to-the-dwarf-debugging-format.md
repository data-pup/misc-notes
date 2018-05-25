# Notes on 'Introduction to the DWARF Debugging Format'

### Overview

This document contains notes Michael Eager's introduction to DWARF. The PDF
can be found [here](http://www.dwarfstd.org/doc/Debugging%20using%20DWARF.pdf).

---

### Introduction

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

