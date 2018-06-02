# DWARF Item Size Calculation Notes

The `DW_AT_byte_size` attribute actually specifies the runtime size of a
struct, so we should not be using that attribute to calculate the size of
a unit in the binary itself. These are subtly different!

Instead, we should find the start and end address of a unit, and then compute
the difference to find the amount of space a unit is taking up. So, how do
we do that?

...

