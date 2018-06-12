# Cargo Notes

In order to make augmentations to how the build process for Cretonne works,
I needed to acquaint myself further with Cargo, how build scripts, their
environment, etc.

Luckily there is a whole book on Cargo! You can read the book that informed
these notes [here](https://doc.rust-lang.org/cargo/index.html).

## Cargo Guide Notes

### Why Cargo Exists

Cargo allows Rust projects to declare their dependencies, and ensure repeatable
build. In order to accomplish this, Cargo does the following:

*  Introduce two metadata files that include various project information
*  Fetches and builds project dependencies
*  Invoke `rustc` with the parameters to build your project
*  Introduce simple conventions to work with Rust projects

### Dependencies

`crates.io` is the Rust community's central package registry. `cargo` is
configured to use this by default to find requested packages.

Dependencies are specified in the `Cargo.toml` file in the `[dependencies]`
section. When `cargo build` is run, Cargo fetches dependencies, along with
their respective dependencies, compiles them all, and updates the `Cargo.lock`
file in the project.

### Project Layout

Cargo uses conventions for file placement in a project, so that navigating
a new Cargo project is easy. The conventions are:

*  `Cargo.toml` and `Cargo.lock` are stored in the package root
*  Source code is placed in the `src` directory
*  The default library file is `src/lib.rs`
*  The default executable file is `src/main.rs`
*  Other executable files can be placed in `src/bin/*.rs`
*  Integration tests go in the `tests` directory
*  Examples go in the `examples` directory
*  Benchmarks go in the `benches` directory

### Cargo.toml vs. Cargo.lock

These files serve two different purposes. To summarize briefly:

*  `Cargo.toml` describes your dependencies in a broad sense, and is written
   by you, the programmer.
*  `Cargo.lock` contains exact information about dependencies, should not
   be manually edited.

Cargo can recalculate dependencies and update the lockfile using `cargo update`

## Cargo Reference Notes

### Environment Variables

Cargo sets and reads a number of environment variables which your code can
detect or override. Here are the environment variables that Cargo sets,
sorted by how and when it uses them.

__Environment Variables for Cargo__

Use these to change Cargo's behavior on your system.

*  `CARGO_HOME` - Location of the registry index and git checkouts of crates
*  `CARGO_TARGET_DIR` - Where to place generated artifacts (relative path)
*  `RUSTC` - Run a specified compiler instead of `rustc`
*  `RUSTC_WRAPPER` - Rustc wrapper
*  `RUSTDOC` - Rustdoc wrapper
*  `RUSTDOCFLAGS` - Custom flags to pass to all rustdoc instances
*  `RUSTFLAGS` - Custom flags to pass to all compiler instances
*  `CARGO_INCREMENTAL` - Set or disable incremental compilation

__Environment Variables for Crates__

These environment variables are exposed to your crate when it is compiled.
Note that this applies for test binaries as well. To get the value of these
variables in a Rust program, use the `env!` macro, like so:

```rust
let version = env!("CARGO_PKG_VERSION");
```

*  `CARGO` - Path to the Cargo binary
*  `CARGO_MANIFEST_DIR` - The directory containing the package manifest
*  `CARGO_PKG_VERSION` - The full version of your package
*  `CARGO_PKG_VERSION_MAJOR` - The major version of your package
*  `CARGO_PKG_VERSION_MINOR` - The minor version of your package
*  `CARGO_PKG_VERSION_PATCH` - The patch version of your package
*  `CARGO_PKG_VERSION_PRE` - The pre-release version of your package
*  `CARGO_AUTHORS` - Colon separated list of authors
*  `CARGO_PKG_NAME` - The name of your package
*  `CARGO_DESCRIPTION` - The description of your package
*  `OUT_DIR` - If the package has a build script, this is set to the folder
   where the build script should place its output. See below.

__Environment Variables for Build Scripts__

When build scripts are run, Cargo sets several environment variables. Note
that because these are not yet set when the build script itself is compiled,
the `env!` macro will not work. Instead, you will need to use the `std::env`
module to retrieve the values of these environment variables when the
build script is run. For example:

```rust
use std::env;
let out_dir = env::var("OUT_DIR").unwrap();
```

The the environment variables exposed to build scripts are as follows:

*  `CARGO` - Path to the Cargo binary
*  `CARGO_MANIFEST_DIR` - Directory containing the package manifet. Also, it
   is the value of `CWD` when the build script starts.
*  `CARGO_MANIFEST_LINKS` - The manifest `links` value.
*  `CARGO_FEATURE_<name>` - Environment variable for each activated feature
*  `CARGO_CFG_<cfg>` - Environment variable for each configuration option
*  `OUT_DIR` - The folder that output should be placed in.
*  `TARGET` - The target triple that is being compiled for
*  `HOST` - The host triple of the rust compiler
*  `NUM_JOBS` - The parallelism max specified
*  `OPT_LEVEL`, `DEBUG` - Values of the corresponding variables for the profile being built
*  `PROFILE` - Set to `release` for release builds, `debug` for other builds
*  `DEP_<name>_<key>` - See the build script documentation for more info on this
*  `RUSTC`, `RUSTDOC` -  The compiler and doc generator that Cargo resolved to use

__Environment Variables for 3rd Party Subcommands__

Programs named `cargo-foobar` placed in `$PATH` are cargo subcommands. These
have access to:

*  `CARGO` - Path to the cargo binary

