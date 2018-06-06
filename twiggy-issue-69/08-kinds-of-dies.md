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

## Chapter 4 - Data Object and Object List Entries

This section presents the DIEs that describe individual data objects,
including variables, parameters, and constants.

*  `DW_TAG_variable`
*  `DW_TAG_constant`
*  `DW_TAG_formal_parameter`
*  `DW_TAG_common_block`
*  `DW_TAG_namelist`
*  `DW_TAG_namelist_item`

## Chapter 5 - Type Entries

*  `DW_TAG_base_type`
*  `DW_TAG_unspecified_type`
*  `DW_TAG_atomic_type`
*  `DW_TAG_const_type`
*  `DW_TAG_immutable_type`
*  `DW_TAG_packed_type`
*  `DW_TAG_pointer_type`
*  `DW_TAG_reference_type`
*  `DW_TAG_restrict_type`
*  `DW_TAG_rvalue_reference_type`
*  `DW_TAG_shared_type`
*  `DW_TAG_violatile_type`
*  `DW_TAG_typedef`
*  `DW_TAG_array_type`
*  `DW_TAG_subrange_type`
*  `DW_TAG_enumeration_type`
*  `DW_TAG_generic_subrange`
*  `DW_TAG_coarray_type`
*  `DW_TAG_structure_type`
*  `DW_TAG_union_type`
*  `DW_TAG_class_type`
*  `DW_TAG_interface_type`
*  `DW_TAG_inheritance`
*  `DW_TAG_access_declaration`
*  `DW_TAG_friend`
*  `DW_TAG_member`
*  `DW_TAG_variant_part`
*  `DW_TAG_condition`
*  `DW_TAG_string_type`
*  `DW_TAG_set_type`
*  `DW_TAG_ptr_to_member_type`
*  `DW_TAG_file_type`
*  `DW_TAG_dynamic_type`
*  `DW_TAG_template_alias`

