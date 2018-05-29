# Gimli and Objects Documentation Notes

## Overview

In order to fix issue 69, we will need to use the `gimli` crate in order
to consume the DWARF debugging information that is included in an ELF
or Mach-O binary. This document contains some notes on the documentation
for this crate. You can find the official documentation here:

`https://docs.rs/gimli/0.15.0/gimli/`

In order to actually parse the binaries, in order to find the information
for the `gimli` crate, we will use the `object` crate. This provides a
cross-platform interface for parsing different object file formats. You
can find the official documentation for `object` here:

`https://docs.rs/object/0.8.0/object/`

## What is `gimli`

This library is a lazy, zero-copy parser for the DWARF debugging information
format. This means that only the items that you iterate over are parsed,
without computing results for items that you did not ask for. 'Zero-copy'
means that no copies of the original input data are ever introduced.

## What is `object`

This crate provides a unified interface for working with object files across
different platforms. This makes it ideal for handling both ELF and Mach-O
binaries, without needing to write code that will handle each individually.

Object files were a new topic to me at this point however, so it bears delving
a little further into what an object file is.

## Object Files

An object file is used to store object data and executable code. These are
divided into separate sections that contain different kinds of data, such
as: Headers containing descriptive information, code segments containing
executable code, data segments containing static variables, dynamic linking
information, and so forth.

We are specifically concerned with ELF, so let's read more about that
specifically!

### ELF (A Very Brief Overview)

ELF (Executable and Linkable Format) is a standard file format used to
represent executable files, object code, and shared libraries, among other
things. It is the standard binary file format for Unix systems using x86
processors.

If you would like to read more about ELF files, you can find a man page
on this by running this command:

```
man 5 elf
```

An executable ELF file consists of an ELF header, followed by a program header
table and/or a section header table. These two tables describe the rest of
the items of the file.

An executable or shared object file's program header table is an array of
structures, each describing a segment or other information used by the system
to prepare the program for execution. Program headers are only useful for
executable or shared object files.

A file's section header table is used to locate all of the sections in the
file.

String table sections hold, well, strings. These represent symbol and section
names. The symbol table holds information to locate and relocate a program's
symbolic definitions and references.

