# DWARF Item Size Calculation Notes

The `DW_AT_byte_size` attribute actually specifies the runtime size of a
struct, so we should not be using that attribute to calculate the size of
a unit in the binary itself. These are subtly different!

Instead, we should find the start and end address of a unit, and then compute
the difference to find the amount of space a unit is taking up. So, how do
we do that? Let's find out by spending time more time reading through the
DWARF v4 specification.

You can find the document [here](http://www.dwarfstd.org/doc/DWARF4.pdf).

## DWARF v4 Spec Notes

The full specification is almost 300 pages, so this will not aim to even
remotely the general DWARF v4 spec. Rather, we are looking specifically for
how we would correctly find the size of a unit in some type of object file,
using the attributes in its DIE (Debugging Information Entry), as well as
how to find the name of an object.

### Attribute Classes

There are a large variety of attributes, but these can be grouped into a set
of different classes. Not all of these are relevant to us at the moment, but
the most pertinent of these groups that we should be aware of are:

*  __hello__ - world
*  __address__ - Refers to some location in the address space of the program.
*  __constant__ - Some sort of data. These can be 1, 2, 4, or 8 bytes of data,
                  or some other size in a variable length format called LEB128.
*  __string__ - A null-terminated sequence of zero or more bytes. Strings can
                be represented directly, or as an offset in the string table.

To see the full table of attribute classes, refer to figure 3 in section 2.

### Entity Size

The `DW_AT_byte_size` specifies the size of a data object or a data type,
measured in bytes. This might seem like a respectable way to find the size of
an object in the binary, and indeed it did initially seem to be to this
programmer.

However, after digging into the specification further, there is an important
detail to consider. Recall that DWARF is designed for supporting debugging
for many different programming languages. In Section 2.19, there is a note
regarding static and dynamic values associated with different attributes.
Of course, different languages have different paradigms, and consequently
different semantics regarding what can and cannot be known at compile time.

So, some of the attributes that apply to types specify a property where the
value may only be known at runtime. The affected attributes include:

*  `DW_AT_bit_size`
*  `DW_AT_byte_size`

"_and possibly others!_"

So, we will need to find another way to calculate the size of an object in
binary, unfortunately.

### Entity Name

The `DW_AT_name` attribute contains a value representing the name of the
object as given in the source program. An especially important point to note
here is that a debugging information entry (DIE) can contain no name attribute,
or contain a name attribute whose value consists of a single null byte, if
the program entity was not given a name in the source program.

This is an important nuance to account for, because it means that not all of
the units might contain this attribute, as well as the fact that the value for
this attribute might also be an empty null byte!

This information can be found at section 2.15 on page 36.

### Address Attributes

Section 2.17 contains some information regarding addresses, and address ranges.
Any DIE that describes an entity that has a location in the machine code may
include one of these attributes. If an entity does not have associated machine
code, none of these will be specified.

The entities that fall into this category include compilation units, module
initialization, subroutines, try/catch blocks, labels, etc. These will have
either:

*  `DW_AT_low_pc` - This describes a single address.
*  `DW_AT_low_pc` and `DW_AT_high_pc` - This pair describes a contiguous range
                                        of addresses.
*  `DW_AT_ranges` - This describes a non-contiguous range of addresses.

## Bloaty Notes

Bloaty is a code size profiler that functions similarly to twiggy. I decided
to review some of the code in this project to learn how it identies the size
of an entity in a binary.

This is a large code base, so I needed to do some digging before I was able
to find what I was looking for, but the most pertinent things were found in
the `dwarf.cc` file. This, as the name suggests, parses the DWARF debugging
information. There are some helper functions that work directly on the
input buffer, which are used elsewhere through the file to find different
attributes and so forth.

Important points worth reviewing further include:
*  `dwarf.cc::ReadDWARFDebugInfo - Line 1900` - Reads attributes in a debugging
   information entry. Note that 'DIEs for compilation units, functions, and
   global variables often have attributes that resolve to addresses.'


