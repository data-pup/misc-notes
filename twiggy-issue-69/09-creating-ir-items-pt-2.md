# Creating IR Items Part 2 Notes

### DIE Parsing Refactoring

After creating the scaffold for a match statement that would process different
types of DIEs according to their tag, it seemed generally impractical, even
with terms categorized using groups from the DWARF specification.

So, instead I think it would be worth using a similar pattern to bloaty, in
the sense that I can consider items according to the existince, or the lack of,
certain attributes. For example, tags that represent some section of executable
machine code might have their location represented via a `low_pc`/`high_pc`
pair, or the `DW_AT_ranges` attribute.

Rather than branching according to tag, and processing these each individually,
we could instead check whether or not a DIE has certain attributes, and pass
them to helper functions based on that :)

### To Do

Review the `dwarfdump` example code again, reading through that code has made
me realize there are also some more elegant ways of working with this data
than the approaches I have been trying so far.

