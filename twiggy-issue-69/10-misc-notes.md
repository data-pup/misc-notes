### Misc Twiggy Progress Notes

```rust
gimli::DW_TAG_variable | gimli::DW_TAG_formal_parameter | gimli::DW_TAG_constant => {
  // ...
}
```

This block in a match statement triggers an error, because it is calling some
code that is currently not yet implemented. This is the stack trace that
I saw.

```
6: twiggy_parser::object_parse::die_ir_item_kind::type_name
at parser/object_parse/die_ir_item_kind.rs:159
7: twiggy_parser::object_parse::die_ir_item_kind::item_kind
at parser/object_parse/die_ir_item_kind.rs:43
8: twiggy_parser::object_parse::die_parse::<impl twiggy_parser::Parse<'unit> for gimli::unit::DebuggingInformationEntry
<'abbrev, 'unit, R, <R as gimli::reader::Reader>::Offset>>::parse_items
at parser/object_parse/die_parse.rs:32
```

The relevant code used to find the type of the variable, parameter, or
constant is here:

```rust
match type_attr {
  gimli::AttributeValue::DebugTypesRef(_) => unimplemented!(),
  gimli::AttributeValue::UnitRef(_) => unimplemented!(),
  _ => Err(traits::Error::with_msg(format!(
        "Unexpected type encoding, found type: {:?}",
        type_attr
    ))),

}
```

Specifically, the arm matching `gimli::AttributeValue::UnitRef(_)` is what
is being thrown here. This is an 'offset into the current compilation unit',
according to the gimli documentation.

The `CompilationUnitHeader` struct has a [entries_at_offset](https://docs.rs/gimli/0.15.0/gimli/struct.CompilationUnitHeader.html#method.entries_at_offset)
method that can be used to navigate the entries in a unit starting at a
specific offset. If we use this an take the first entry from that iterator,
we will have the DIE that represents the type of this variable! (I think?)

Alternatively, this can return a `gimli::AttributeValue::UnitRef(_)`, which is
'an offset into the current `.debug_info` section, but possibly a different
compilation unit from the current one.'

For now I am going to start by implementing the logic that will be used to
work with the offset into the current compilation unit.

