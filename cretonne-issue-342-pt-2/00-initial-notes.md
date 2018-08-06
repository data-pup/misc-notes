# Initial Notes

Before diving fully into working on this, it would be prudent to read through
the existing code thoroughly, and gain an understanding of what is happening.
As mentioned in the overview, we will split the target-specific settings
out into a separate step in our new version, so that we do not need to deal
with the ISAs.

Consequently, we will need to identify what parts of the code relate to general
settings and which parts are used by the isa-specific settings.

Reading through this further, I am also seeing how useful procedural macros
will become, once Cranelift no longer needs to support 1.25. In the meantime
however, we can do our best to replicate the existing functionality.

### Dependencies

Before filtering things down to solely the general settings code, I want to
identify ~all~ of the dependencies used by the `gen_settings.py` file.

The different imports in this file specifically are:
*  `from __future__ import absolute_import`
*  `import srcgen`
*  `from unique_table import UniqueSeqTable`
*  `import constant_hash`
*  `from cdsl import camel_case`
*  `from cdsl.settings import BoolSetting, NumSetting, EnumSetting`
*  `from base import settings`

Some of these, such as the import from `__future__` are not things we will
have to worry about. Others, such as `srcgen` have already had some
non-trivial work performed in the last step.

The `camel_case` function was implemented in my last PR as well, having
expected that it would come into play shortly. This means that the bulk of
work should be confined to the following:

*  `from unique_table import UniqueSeqTable`
*  `import constant_hash`
*  `from cdsl.settings import BoolSetting, NumSetting, EnumSetting`
*  `from base import settings`

Current questions that I have about these imports:
*  Is the `unique_table` module an external one? If so, what would this
   be equivalent to within my Rust reimplementation? Hashtable?
*  Is the `constant_hash` module an external one? See above. If these -are-
   external, and not internal to the project, I should figure out what the
   equivalent would be in Rust. Probably a `HashMap` or `HashSet` by the looks
   of it.

The others, `cdsl.settings` and `base.settings` are straightforward in that
they are certainly new files I will add and refactor.

### Avoiding Mutable Static Variables

As mentioned in the initial overview of this work:

> Note that SettingsGroup's constructor registers the created object in a static
> `_current` variable, and all the settings are automatically collected from
> Python's globals() at the end. This is an example of the "DSL" approach, and
> it does make the file a little more streamlined. A less magical approach would
> be to just add settings to a SettingsGroup object explicitly, which would also
> be more Rust-friendly.

Rather than using Python magic, and mutable global state, we will create some
sort of `SettingsGroup` object explicitly. This will be easier to create in
Rust, since static variables are not mutable in Rust.

### Adding Settings Classes/Structs

Similar to the manner in which the types were implemented, the settings will
require a separate file in both the `base` and `cdsl` modules. The first is
responsible for 'instantiating' the settings, and the `cdsl` file is
responsible for defining the classes themselves.

The Python implementation here also heavily relies on some class inheritance,
which can be represented within the Rust code using `enums`.

