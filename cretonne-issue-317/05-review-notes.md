## PR Review Notes

### Overview

The initial steps I took are on the `optimize-0.0-constants-pr-q` branch,
which are themselves rebased/squashed changes that were made on the
`optimize-0.0-constants-dev` branch.

Continuing work will be done on the `optimize-0.0-constants-dev-contd` branch.

Below are the notes that were taken from the review, for commit
`c3060b16ef8e60c89127b6c21a531a5588cb6416` on the `pr-q` branch.

### Review Notes

__Test Failure:__

The test is currently failing because there is not a recipe registered for the
case that we are testing. (This is good!) This is because the first step is
registering the recipe, the second is to add an encoding rule that uses
that recipe.

See the code for `base.iconst.i32` in `lib/codegen/meta/isa/x86/encodings.py`
for examples of what this looks like.

__Typo Note:__ Be sure to fix the typo in the `meta/cdsl/predicates.py` type
notation comment for `IsZero.__init__`.

__Recipe Emit Note:__ For the `emit` parameter for the new recipe that was
added to `meta/isa/x86/recipes.py`, look to the `fa` recipe for guidance. This
will end up looking very similar to that.

__Recipe Comment Note:__ In the comment for the new recipe, specify that this
will only apply to 0.0 immediates, and not for other floating point immediates.

### To-Do Items

Tasks to complete, based off of the review notes:

- [x]  Fix typo in the `IsZero.__init__` method.
- [x]  Add details / remove fixup note from the new recipe comment.
- [x]  Add emit details for the new recipe, following `fa` as guidance.
- [x]  Register the recipe.
- [x]  Add an encoding rule that uses the recipe.

### Notes

Relevant lines in the codegen crate for registering a recipe include:

*  `meta/base/legalize.py` - Line 78 - Registers the `expand_fconst` function
*  `src/legalizer/mod.rs` - Line 245 - Defines the `expand_fconst` function

Defining an encoding rule using a recipe:

```
See the code for `base.iconst.i32` in `lib/codegen/meta/isa/x86/encodings.py`
for examples of what this looks like.
```

### Fixing the Predicate Logic

Once I was (somewhat) finished with the steps above, I had some different
errors to contend with to get this working. Namely, the `is_zero` function
that I added in some of the previous commit would need to change.

Here are the notes that were given on the predicate function:

```
Oops, I was mistaken when I suggested using is_sign_positive. Cretonne
represents f32 and f64 immediate values using Ieee32 and Ieee64, which have a
bits() function that returns the bits as u32/u64. So we can test for positive
zero by just doing .bits() == 0.
```

Neat! So this changes things slightly, but shouldn't entail too much work.

