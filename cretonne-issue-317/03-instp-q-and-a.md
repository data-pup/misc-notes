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

`encodings:189` shows encoding rules for integer constants. We should perform
a similar encoding process for floating point values.

`lib/codegen/meta/cdsl/predicates.py` defines `instp` predicates. We will need
to use this on our recipe to check that the immediate value is zero.
`is_sign_positive` will be helpful here for distinguishing between negative
and positive zero. This will -probably- be done within the `rust_predicate`
method?

NOTE: We may need to -add- a new predicate for this?

__Adding a new predicate:__

If this should be anything like the `IsSignedInt` or `IsUnsignedInt`
predicates, we will need to make a class that inherits from `FieldPredicate`,
which is defined at line 202 of `predicates.py`.

### Questions ?

*  The test case as is, does not fail without any changes, how can I make sure
   that the optimization is working?
*  The `outs` section is a little opaque to me at the moment: What does "uses
   its output register for the x86 instruction encoding inputs" mean?
*  modrm bytes, and that notation in general, is still a new concept to me.
   What should I name this recipe? What should I place in the name?
*  Should this have a predicate? I am having trouble seeing where the
   `is_sign_positive` function should be placed.

__Answers__

Got some answers regarding the questions above :)

*  Check `filetests/isa/x86/binary64.cton` for example on how to test this :)
*  `outs` will be an FPR, `ins` will be empty, since it is an immediate value.
*  I figured this out above, but indeed I will want to make a new subclass of
   FieldPredicate.

---

`FieldPredicate.__init__` args:

```
:param field: The `FormatField` to be tested.
:param function: Boolean predicate function to call.
:param args: Additional arguments for the predicate function.
```

Note: The `function` should be the name of a function that is found in
`codegen/src/predicates.rs` :)

