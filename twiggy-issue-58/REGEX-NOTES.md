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

__Refactoring `functions` Initialization__

The functions in `analyze` are responsible for creating an object that will
be processed by functions inside of a corresponding `impl traits::Emit` block.
These blocks implement functions to emit to plain text, Json, and eventually
other formats.

In our case, the important component is initializing a sorted `Vec<ir::Id>`
object, representing the items whose retaining paths we will emit. This
ignores the details of how to emit output in one of these formats, which makes
things somewhat simpler.

What did initially need to change is some of the structure regarding how we
fill this collection. Specifically, we have to deal with the case of when
there are no argugments given, depending on the direction we are traversing
the paths. For readability, this was divided into some separate closures,
because regexps will make the case when arguments were given to the sub-command
more complex.

This mostly involved moving existing code, but closures are a really neat
concept in Rust.

__Regexps include other items!__

Once I had a basic draft of the regexp code in place (we will cover error
handling below in further detail), a noticable problem popped up when the
code was tested. The problem being that regular expressions would match other
items in the binary! This caused a number of other test cases to fail, which
is decidedly not good.

__Error Handling__

The important thing to note here is that creating a Regex object from a string
can fail. This will happen if the string does not contain a valid pattern,
and means that we will need to be sure that this case is handled properly.

The nice thing about `unwrap` is that it means we can think about this later,
and simply unwrap it for now. We can move on and work on different high level
issues in the code, and come back to properly handle this case when we have
a better idea of what our solution will look like.

(Todo...)



