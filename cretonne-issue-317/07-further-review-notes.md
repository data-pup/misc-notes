# Review Notes

At commit `996d70f5b4e7d0dadd5cf49e480909df36c1ad44`, there were some further
details to iron out. The to-do list is as follows at the moment:

To-Do:
*  filetests/isa/x86/optimized-zero-constants-32bit.cton - Add commas between
   the two operands for `xorps`
*  filetests/isa/x86/optimized-zero-constants.cton - Change the instruction
   operation into `xorpd`, to reflect the changes made in the code.
*  lib/codegen/meta/isa/x86/encodings.py - There is an extraneous comma at
   line 616, for the 32-bit float.
*  lib/codegen/src/predicates.rs - Notes below.

### Predicate Changes

...

