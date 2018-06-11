### Cretonne Build System Alterations Notes

The comments in the file do give a good general overview of the build script,
including its purpose, and the environment it is run inside of:

```
// Build script.
//
// This program is run by Cargo when building lib/codegen. It is used to generate Rust code from
// the language definitions in the lib/codegen/meta directory.
//
// Environment:
//
// OUT_DIR
//     Directory where generated files should be placed.
//
// TARGET
//     Target triple provided by Cargo.
//
// CRETONNE_TARGETS (Optional)
//     A setting for conditional compilation of isa targets. Possible values can be "native" or
//     known isa targets separated by ','.
```

The general idea of the script is covered in the notes document, so we won't
discuss that much further because at the moment, my concern is regarding
how to generate a new file using the files that I added. Because these are
written in Rust rather than Python, this will mean adding some new logic.

