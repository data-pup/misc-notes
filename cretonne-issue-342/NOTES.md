# Cretonne Issue 342 Implementation Notes

First, implement a Rust version of `srcgen.py`, which the `gen_types.py` file
depends on. Once this is complete, implement a Rust version of the
`gen_types.py` file.

## `srcgen` Re-implementation Notes

As mentioned in the `README`, the first step towards getting this issue solved
will entail rewriting the `srcgen` file in Rust. This file is where the bulk
of the work will be, as this file contains more logic than the `gen_types`
file itself.

### `srcgen` Overview

The first step was reading through the source of this file and gaining an
understanding of the file. In general, this contains some various classes
used for emitting/generating code, including things like tracking indentation
level, emitting comments, etc.

*  class `Formatter`
  * class `_IndentedScope`
  *  `__init__(self)`
  *  `indent_push(self)`
  *  `indent_pop(self)`
  *  `line(self, s=None)`
  *  `outdented_line(self, s)`
  *  `writelines(self, f=None)`
  *  `update_file(self, filename, directory)`
  *  `indented(self, before=None, after=None)`
  *  `format(self, fmt, *args)`
  *  `multi_line(self, s)`
  *  `comment(self, s)`
  *  `doc_comment(self, s)`
  *  `match(self, m)`
*  class `Match`
  *  `arm(self, name, fields, body)`
