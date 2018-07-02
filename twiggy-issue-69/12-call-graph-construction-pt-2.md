# Call Graph Construction Notes Pt. 2

I spent some more time reading through the DWARF specification, and found
this useful tidbit in Chapter 3.3.1.3.

> The `DW_AT_call_all_source_calls` attribute indicates that every call that
> occurs in the code for the subprogram, including every call inlined into it,
> is described by either a `DW_TAG_call_site` entry or a
> `DW_TAG_inlined_subroutine` entry; further, any call that is optimized out is
> nonetheless also described using a `DW_TAG_call_site` entry that has neither a
> `DW_AT_call_pc` nor `DW_AT_call_return_pc` attribute.
>
> *The `DW_AT_call_all_source_calls` attribute is intended for debugging
> information format consumers that analyze call graphs.*
>
> If the the `DW_AT_call_all_source_calls` attribute is present then the
> `DW_AT_call_all_calls` and `DW_AT_call_all_tail_calls` attributes are also
> implicitly present. Similarly, if the `DW_AT_call_all_calls` attribute is
> present then the `DW_AT_call_all_tail_calls` attribute is implicitly present.

To summarize into a few bullet points relevant to us:

*  For a new subroutine item, check that `DW_AT_call_all_source_calls` is
   set.
*  Assuming the above flag is set, every call to some other subroutine in the
   code for this subprogram is described by a `call_site` or a
   `inlined_subroutine` tagged DIE.
*  Subroutine that were optimized out will be marked by a `call_site` DIE
   that has neither a `call_pc` nor a `return_pc` attribute.

**NOTE:** I have some editing to do regarding the `DW_AT_entry_pc` attribute.
          This is distinct from the low_pc attribute, and refers to the first
          *executable* instruction. (**UPDATE**: Done.)

### Identifying edges

...

