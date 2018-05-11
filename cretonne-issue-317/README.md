Cretonne currently lowers all floating-point constant instructions by emitting
integer constants with the desired bit patterns and then moving them into
floating-point registers. The code that does this is here.

For reference, the `expand_fconst` function used here is registered here.

However, most CPUs have faster ways to set registers to zero. For example, on
x86, one can do xorps %xmm0, %xmm0. Xor'ing a register with itself seems
roundabout, but it turns out to be common enough that CPUs recognize it
specially, and often optimize it in register renaming rather than actually
executing it as an instruction.

A simple way to implement this for x86 would be to define an encoding rule for
x86 similar to the rules for integer constants, using an instp predicate to
test for the value of 0. Be aware that in IEEE 754 floating-point, negative
zero compares equal to zero, but is a distinct bit pattern; the
`is_sign_positive` function may be useful in distinguishing between the two.

Cretonne has encodings for xorps:
```
https://github.com/cretonne/cretonne/blob/master/lib/codegen/meta/isa/x86/encodings.py#L595
https://github.com/cretonne/cretonne/blob/master/lib/codegen/meta/isa/x86/recipes.py#L332
```

which may be useful examples, though the recipe as written is only usable for
Binary operators, so I think the best way to proceed here is to add a new
recipe.

Here's a simple testcase for f32:

```
function %foo() -> f32 fast {
  ebb0:
      v0 = f32const 0.0
          return v0
}
```

