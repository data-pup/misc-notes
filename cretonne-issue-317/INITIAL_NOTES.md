# 0.0 Constant Optimization

## Problem

Currently, floating-point constant instructions are lowered by emitting
unsigned integers containing the desired bit pattern, and moving them into
the floating point register.

However, most CPUs have faster ways to zero out a register. For example, on
x86 you can use `xorps %xmm0 %xmm0`. This is a common enough action that most
CPUs recognize it specifically and optimize it during register renaming,
rather than actually executing it.

## Solution

Implement this optimization for x86 by defining an encoding rule. Similar to
the rules for integer constants, use an `instp` predicate to check for the
value of zero. Note that for IEEE 754 floating-point, negative zero is a
distinct bit pattern. Use the `is_sign_positive` function to distinguish.

`README` contains paths to existing encodings/recipes for `xorps`.

```
/lib/codegen/meta/isa/x86/encodings.py (check line #595)
/lib/codegen/meta/isa/x86/recipes.py (check line #332)
```

Note that these are only usable for Binary operators, so we will need to
define a new recipe.

A test case is also included in `README`.

