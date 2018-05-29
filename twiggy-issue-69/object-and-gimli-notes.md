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

...

