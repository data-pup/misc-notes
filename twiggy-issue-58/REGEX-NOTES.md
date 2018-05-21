# Regex Arguments Notes

### Overview

Adding regex functionality to the arguments for the functions is somewhat
non-trivial, so this document will cover notes regarding the considerations
and implementation details of adding this to the `twiggy paths` sub-command.

Some of these issues are covered at a higher level of abstraction in the
initial notes document, this document is intended to cover more of the detailed
issues regarding adding this feature.

---

__Regexps and Subcommand Options__

NOTE: Potentially, we might want to change the name of the `functions` member
of the `twiggy_opts::Paths` struct. This will now refer to patterns, which
might not always be the specific name of function. For now, we will leave
this alone.

Further, depending on where we would like to start considering the arguments
given to the subcommand as regexes might mean dragging the regex dependency
into the opts crate as well. For now, I will be moving with the option that
involves the least impact to the code, meaning that only the analyze functions
will be responsible for unwrapping these arguments and converting them into
Regex objects.

__Error Handling__

(To do...)

