At this point, I have introduced a new predicate, and a new recipe. These will
be due for some checking, as I am unsure about certain notation details, but
the scaffolding is at least in place now.

The issue description mentions that the `expand_fconst` function is what
currently lowers values into floating point registers. We will need to register
the new recipe, and then to call it in cases where the predicate is satisfied.

Relevant lines in the codegen crate for this will include:
*  `meta/base/legalize.py` - Line 78 - Registers the `expand_fconst` function
*  `src/legalizer/mod.rs` - Line 245 - Defines the `expand_fconst` function

The other thing we will need to do will include adding test notation to the
test case so that we can verify that a `xorps %xmm0 %xmm0` instruction is
actually being generated. As mentioned before, we can see some examples of this
in `filetests/isa/x86/binary64.cton`.

As of now this seems to be the general roadmap.

*  [ ] - Register the new recipe.
*  [ ] - Add test notation.
*  [ ] - Fix any discovered bugs.

From there, it will be a matter of merging into a new branch, cleaning up the
dev history, and submitting a pull request. Be sure to review the fixup notes
that I have left for myself before opening this.

