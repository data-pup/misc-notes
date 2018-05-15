# Implementation Notes

### Overview

This document contains notes on implementing a solution to solve issue #317.

### Test Case

A simple test case is here:

```
function %foo() -> f32 fast {
ebb0:
  v0 = f32const 0.0
  return v0
}
```

There is some more detailed information about testing cretonne in
`docs/testing.rst`.

### Recipe

Recipe for `UnaryImm` operators, with no declared register inputs, uses its
output register for the x86 instruction encoding inputs.

### PR Questions ?

*  The test case as is, does not fail without any changes, how can I make sure
   that the optimization is working?
*  The `outs` section is a little opaque to me at the moment: What does "uses
   its output register for the x86 instruction encoding inputs" mean?
*  modrm bytes, and that notation in general, is still a new concept to me.
   What should I name this recipe? What should I place in the name?
*  Should this have a predicate? I am having trouble seeing where the
   `is_sign_positive` function should be placed.

