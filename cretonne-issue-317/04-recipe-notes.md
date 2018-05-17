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

*  [x] - Register the new recipe.
*  [x] - Add test notation.
*  [ ] - Fix any discovered bugs.

From there, it will be a matter of merging into a new branch, cleaning up the
dev history, and submitting a pull request. Be sure to review the fixup notes
that I have left for myself before opening this.

---

### Test Notation

Before I wire in the new encoding, I should flesh out the filetest test case
evaluation. These use `filecheck`, a Rust implementation of an LLVM tool of
the same name. For more information about this tool and its syntax, go to:
https://docs.rs/filecheck

This will involve using `test binemit`, which tests the emission of
binary machine code. To enable this, add `test binemit` to the top of the file.

Here is an example from the Cretonne documentation:

```
test binemit
isa riscv

function %int32() {
ebb0:
  [-,%x5]             v0 = iconst.i32 1
  [-,%x6]             v1 = iconst.i32 2
  [R#0c,%x7]          v10 = iadd v0, v1       ; bin: 006283b3
  [R#200c,%x8]        v11 = isub v0, v1       ; bin: 40628433
  return
}
```

`binary32-float.cton` and `binary64-float.cton` both contain some examples as
well, and we can probably get some help with deriving the correct encoding for
the instruction from these.

### `xorps` Encoding

This is worth understanding in better depth, so let's look at two of the
places where `xorps` appears in the existing tests. As mentioned above, these
are in both the 32-bit and 64-bit floating point test files.

Here are two places where this operation is used, and the expected encoding.

__32-bit:__

```
; asm: xorps %xmm2, %xmm5
[-,%xmm5]           v36 = bxor v10, v11                     ; bin: 0f 57 ea
; asm: xorps %xmm5, %xmm2
[-,%xmm2]           v37 = bxor v11, v10                     ; bin: 0f 57 d5
```

__64-bit:__

```
; asm: xorps %xmm10, %xmm5
[-,%xmm5]           v36 = bxor v10, v11                     ; bin: 41 0f 57 ea
; asm: xorps %xmm5, %xmm10
[-,%xmm10]          v37 = bxor v11, v10                     ; bin: 44 0f 57 d5
```

### Breaking down 32-bit xorps instructions:

Let's start with the 32-bit encoding.

```
ASM:    xorps %xmm2 %xmm5
Hex:    0    f    5    7    e    a
Binary: 0000 1111 0101 0111 1110 1010

ASM:    xorps %xmm5 %xmm2
Hex:    0    f    5    7    d    5
Binary: 0000 1111 0101 0111 1101 0101
```

The opcode of `xorps` is `0F 57 /r`, so we are already 2/3 of the way there!
The next step is understanding what the `ea` and `d5` components are. What
is that `/r` and what does it signify?

This is a complex topic, but read further on 'ModR/M' and `SIB` bytes for
more information. In short, "/r — Indicates that the ModR/M byte of the
instruction contains a register operand and an r/m operand."

This byte is structured like so:

```
  7   6   5   4   3   2   1   0
  +---+---+---+---+---+---+---+---+
  |  mod  |    reg    |     rm    |
  +---+---+---+---+---+---+---+---+
```

If 'mod' is set to 'b11', then register-direct addressing mode is used
(in general). So, we can break down the instructions above like so:

```
xorps %xmm2 %xmm5

  Opcode             mod reg reg
+--------------------+---+---+---+
| 0000 1111 0101 0111 11  010 101|
+--------------------+---+---+---+
  0    F    5    7        2   5
```

__Zero Constants__

So, if we are trying to assign a zero constant to a floating point register
using the `xorps` instruction on the same register (xmm0), than we should
expect an instruction that looks like this:

```
xorps %xmm0 %xmm0

  Opcode             mod reg reg
+--------------------+---+---+---+
| 0000 1111 0101 0111 11  000 000|
+--------------------+---+---+---+
  0    F    5    7
```

The last two bytes in this instruction would be `1100 0000`, or `0xC0`. This
would mean that the overall instruction would be encoded as `0x0F57C0`.

Neat! Now, let's look into the 64-bit equivalent of this instruction.

### Breaking down 64-bit xorps instructions:

Todo ...

```
ASM:    xorps %xmm10, %xmm5
Hex:    4    1    0    f    5    7    e    a
Binary: 0100 0001 0000 1111 0101 0111 1110 1010

ASM:    xorps %xmm5, %xmm10
Hex:    4    4    0    f    5    7    d    5
Binary: 0100 0100 0000 1111 0101 0111 1101 0101
```

### Registering the functions

Todo ...

### References

```
https://www.felixcloutier.com/x86/XORPS.html

https://wiki.osdev.org/X86-64_Instruction_Encoding#ModR.2FM_and_SIB_bytes

https://stackoverflow.com/questions/15017659/how-to-read-the-intel-opcode-notation

https://en.wikipedia.org/wiki/X86_instruction_listings
```
