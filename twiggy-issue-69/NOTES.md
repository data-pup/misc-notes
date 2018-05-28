# Twiggy Issue 69 Notes

### Adding Dependencies

As mentioned in the README, the first step in this project will be
constructing the call graph, using the `DW_AT_call_site` attribute. Before
that can be done however, I need to add dependencies for the crates that
will help consume the object file information for ELF and Mach-O files.

These should be added to the specific crates within the workspace that will
actually need to refer to these functions/classes/methods/etc.

