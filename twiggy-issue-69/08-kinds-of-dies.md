# Kinds of DIEs

There is a wide set of `DW_TAG_something` tags, which are explained in Chapters
3, 4, and 5 of the DWARF spec. This document contains notes on which are
covered in each chapter, to help me categorize these and assign an `ItemKind`
value.

## Chapter 3 - Program Scope Entries

These DIEs relate to different levels of program scope: compilation, module,
subprogram, etc. Except for separate type entries, these are ranges of text
addresses within the program, and thus map most closely to the `Code` type.

*  `DW_TAG_compile_unit`
*  `DW_TAG_partial_unit`
*  `DW_TAG_type_unit`
*  `DW_TAG_skeleton_unit`
*  `DW_TAG_module`
*  `DW_TAG_namespace`
*  `DW_TAG_imported_declaration`
*  `DW_TAG_imported_module`
*  `DW_TAG_imported_unit`
*  `DW_TAG_subprogram`
*  `DW_TAG_inlined_subroutine`
*  `DW_TAG_inlined_entry_point`
*  `DW_TAG_call_site`
*  `DW_TAG_lexical_block`
*  `DW_TAG_label`
*  `DW_TAG_with_stmt`
*  `DW_TAG_try_block`
*  `DW_TAG_catch_block`

__NOTE: Not sure if these should be mentioned here:__
*  `DW_TAG_thrown_type`
*  `DW_TAG_variable`
*  `DW_TAG_formal_parameter`
*  `DW_TAG_member`
*  `DW_TAG_call_site_parameter`
*  `DW_TAG_unspecified_parameters`

## Chapter 4 - Data Object and Object List Entries

This section presents the DIEs that describe individual data objects,
including variables, parameters, and constants.

*  `DW_TAG_variable`
*  `DW_TAG_constant`
*  `DW_TAG_formal_parameter`
*  `DW_TAG_`
*  `DW_TAG_`
*  `DW_TAG_`
*  `DW_TAG_`
*  `DW_TAG_`
*  `DW_TAG_`
*  `DW_TAG_`
*  `DW_TAG_`

