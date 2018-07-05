### Misc Notes i.

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

---

### Misc Notes ii.

**Rust Project Layout**

At the point the current topography looked roughly like this:
*  __lib/codegen-meta/src/base/types.rs:__ This defines the enums for the
   scalar types.
*  __lib/codegen-meta/src/cdsl/types.rs:__ This defines the structs etc. themselves.

**Special Types**

I spent some time today focusing on the special values, and considering how
I could get some of the type emission working. After some fair amount of
floundering about with various competing ideas, I seemed to have some luck
using helper structs that would be used to iterate through the potential
options for the different values of enums.

This was where I started to gain some understanding of the concept that
procedural macros would eventually be useful for this, but I am still wrapping
my head around that notion at the moment.

```
5: std::panicking::begin_panic
  at /checkout/src/libstd/panicking.rs:445
6: cretonne_codegen_meta::cdsl::types::ValueType::rust_name
  at lib/codegen-meta/src/cdsl/types.rs:41
7: cretonne_codegen_meta::gen_types::emit_type
  at lib/codegen-meta/src/gen_types.rs:21
8: cretonne_codegen_meta::gen_types::emit_types
  at lib/codegen-meta/src/gen_types.rs:46
9: cretonne_codegen_meta::gen_types::generate
  at lib/codegen-meta/src/gen_types.rs:68
10: build_script_build::main
  at lib/codegen/build.rs:81
```


**Lane Types**

This will be completed once I have the special types laid out.

**Vector Types**

Regarding the emission of vector types, this is the stack trace I am at right
now, aka the state of progress atm.

```
6: cretonne_codegen_meta::gen_types::emit_vectors
at lib/codegen-meta/src/gen_types.rs:36
7: cretonne_codegen_meta::gen_types::emit_types
at lib/codegen-meta/src/gen_types.rs:51
8: cretonne_codegen_meta::gen_types::generate
at lib/codegen-meta/src/gen_types.rs:64
9: build_script_build::main
at lib/codegen/build.rs:81
```

This is thrown by this Rust code in `gen_types.rs`.

```rust
6: cretonne_codegen_meta::gen_types::emit_vectors
  at lib/codegen-meta/src/gen_types.rs:36
7: cretonne_codegen_meta::gen_types::emit_types
  at lib/codegen-meta/src/gen_types.rs:51
8: cretonne_codegen_meta::gen_types::generate
  at lib/codegen-meta/src/gen_types.rs:64
9: build_script_build::main
  at lib/codegen/build.rs:81
```

