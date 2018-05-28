# Twiggy Issue 69 Notes

### Adding Dependencies

As mentioned in the README, the first step in this project will be
constructing the call graph, using the `DW_AT_call_site` attribute. Before
that can be done however, I need to add dependencies for the crates that
will help consume the object file information for ELF and Mach-O files.

These should be added to the specific crates within the workspace that will
actually need to refer to these functions/classes/methods/etc.

### Test Fixtures

Before I stepped into any of the trait implementation for parsing ELF or
Mach-O binaries, I should be sure that I can compile a test fixture into
each of these formats, with DWARF debugging information included in the
resulting binary.

You can print a list of targets that are available for `rustc`, using this:

```sh
rustc --print target-list
```

Other helpful tricks I found were how to print codegen options, using:

```sh
rustc -C help
```

These end up printing a lot of information, so it can be helpful to pipe
this into `less` so you can page through the info and search for terms.

__Adding a compilation target:__

The `rustup` command can be used to add a target to your Rust toolchain.
For more detailed information regarding this, use `rustup target --help`.

I added the targets required to build my fixtures using this:

```sh
rustup target add x86_64-apple-darwin
```

However, when I cross-compiled the `hello_world.rs` fixture, I discovered a
new error. Interestingly, `cc` failed. This seemed peculiar.

```
█  ✨  rustc +nightly -g --target x86_64-apple-darwin hello_world.rs -o hello_mach.o -C lto=fat -C opt-level=z
error: linking with `cc` failed: exit code: 1
  |
  = note: "cc" "-m64" "-L" "/home/user/.rustup/toolchains/nightly-x86_64-unknown-linux-gnu/lib/rustlib/x86_64-apple-darwin/lib" "hello_mach.hello_world0.rcgu.o" "-o" "hello_mach.o" "-Wl,-dead_strip" "-nodefaultlibs" "-L" "/home/user/.rustup/toolchains/nightly-x86_64-unknown-linux-gnu/lib/rustlib/x86_64-apple-darwin/lib" "/tmp/rustc.NqZk136kpuEx/libstd-bb4de3b6fa007f70.rlib" "/tmp/rustc.NqZk136kpuEx/liballoc_jemalloc-1ca6ecef640e20f9.rlib" "/home/user/.rustup/toolchains/nightly-x86_64-unknown-linux-gnu/lib/rustlib/x86_64-apple-darwin/lib/libcompiler_builtins-6187294e7e5a1734.rlib" "-l" "System" "-l" "resolv" "-l" "pthread" "-l" "c" "-l" "m"
  = note: hello_mach.hello_world0.rcgu.o: file not recognized: File format not recognized
          collect2: error: ld returned 1 exit status

error: aborting due to previous error
```

