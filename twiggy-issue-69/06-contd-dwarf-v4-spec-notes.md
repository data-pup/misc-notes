# DWARF v4 Specification Notes (Contd.)

## Section 2

Each DIE consists of an identifying tag and a series of attributes. A single
entry, or a group of entries, provide a description of a corresponding entity
in the source program. The tag specifies the class to which an entry belongs.

Examples of some tags include `DW_TAG_variable` `DW_TAG_member`,
`DW_TAG_subprogram`, and `DW_TAG_base_type`.

NOTE: More through notes on this section are included in
`05-fixing-item-size-calculations.md`.

Let's read more about the specific types of debugging information entries, that
represent different abstractions within a program.

## Section 3 - Program Scope Entries

Program scope entries relate to different levels of program scope.

### Unit Entries

An object file can contain multiple compilation units. There are three kinds of
compilation units: normal compilation units, partial compilation units, and
type units.

A partial compilation unit is related to one or more other compilation units
that import it, while a type unit represents a single complete type in a
separate unit.

A normal or partial compilation unit may be logically incorprated into another
compilation unit using an imported unit entry.

__Normal/Partial Compilation Unit Entries__

A normal compilation unit is represented by a DIE with `DW_TAG_compile_unit`
tag. A partial compilation unit DIE is tagged with `DW_TAG_partial_unit`.

A normal compilation unit typically represents the text/data contributed by a
single relocatable object file. This can be derived from several source files.
A partial compilation unit typically represents a part of the text and data in
a relocatable object file.

A compilation unit entry owns debugging information entries that represent all
or part of the declarations made in the corresponding compilation.

Compilation unit entries may have the following attributes:

1. Either a `DW_AT_low_pc` and `DW_AT_high_pc` pair or a `DW_AT_ranges`
   attribute whose values encode the address ranges of machine code from this
   unit.
2. A `DW_AT_name` attribute whose value is the full or relative path name of
   of the primary source file from which the unit was derived.
3. A `DW_AT_language` attribute w/ a constant indicating the source language.

Other attributes that may be present include:

*  `DW_AT_stmt_list` - used for line number information
*  `DW_AT_macro_info` - used for macro information
*  `DW_AT_comp_dir` - string containing CWD for this unit's compilation command
*  `DW_AT_producer` - string containing information about the compiler
*  `DW_AT_identifier_case` - constant representing treatment of identifiers
*  `DW_AT_base_types` - used to find the definitions of types in other units
*  `DW_AT_use_UTF8` - flag indicating whether or not names etc. use UTF8
*  `DW_AT_main_subprogram` - flag representing if this is contains a subprogram marked as the main entry point.

### Imported Unit Entries

`DW_TAG_imported_unit` tagged entries represent when a normal or partial unit
is imported. This entry will contain a `DW_AT_import` attribute whose value is
a reference to another compilation unit whose declarations are needed.

This entry does not necessarily correspond to any entity or construct in the
source. It is 'glue' used to relate a unit to a place in some other compilation
unit.

### Separate Type Unit Entries

An object file can contain any number of separate type unit entries,
representing type definitions. Each type unit must be uniquely identified by
a 64-bit signature, stored as part of the type unit. This can be used to
reference the type definition from DIEs in other compilation units and type
units.

Type units are represented by a DIE with the `DW_TAG_type_unit` tag.

In general, only large types such as structure, class, enumeration, and union
types should be considered for separate type units. Base types etc. are not
worth the overhead of placement in separate type units.

Types that are unlikely to be replicated, such as types in the main source
file, are also better left in the main compilation unit.

