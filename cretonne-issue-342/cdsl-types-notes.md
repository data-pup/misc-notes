Before I could reimplement the cdsl types in Rust, I realized that I would need
to spend some more time understandling/notating the structure of the types file
to think about how this could be rebuilt.

Importantly, Rust will not allow the same inheritance structure to be created,
so we will need to build something synonymous using traits etc.

Traits were not quite working for me in my initial structure of things,
because these cause issues related to the `Sized` trait when we try to
place them together in a single collection.

Instead, I currently think there is a better design scheme that involves a tag,
and considering the scalar types that can be placed into vector lanes
separately from the ValueType subclasses like VectorType or SpecialType.

