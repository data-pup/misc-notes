# Issue 146 Solution Implementation Notes

The overview for the solution to this issue is covered in `README.md`. This
document covers each of these steps in further detail.

## Solution Steps

Here is what we will need to do:

### Step 1.a - Find the `wasm-bindgen` dependency in `Cargo.toml`

Use `manifest::read_cargo_toml(path: &str)` to create a `CargoManifest` object.
Then, check if the `dependencies` member is `Some(dep: CargoDependencies)`.
If so, check if the `wasm_bindgen` member is `Some(ver: String)`.

If this does not exist, then we should probably return an error of some sort,
since `wasm-bindgen` should (must?) be in the crate manifest's dependencies.

### Step 1.b - Create an expected `--version` result string

Use this value with the `format!` macro to create an expected version string.

### Step 2 - Check if `wasm-bindgen` is installed locally within the project

Check if the dependency exists at this relative path: `./bin/wasm-bindgen`

If not, it was not installed locally, and we should install it. (Step 4)
Otherwise, proceed to Step 3.a.

### Step 3.a - Run `wasm-bindgen --version` and capture the output

Shell out and run this command:

```sh
./bin/wasm-bindgen --version
```

### Step 3.b - Check if one of the installed versions are the correct version

Check if the string generated in Step 1.b matches the string created in
Step 3.a. If not, proceed to Step 4. If the strings do match, we are finished,
and the locally installed version is correct.

### Step 4 - Install `wasm-bindgen` locally if required

The `wasm-bindgen-cli` dependency can be installed using this command.

```sh
cargo +nightly install -f wasm-bindgen-cli \
    --version "$version" \
    --root "$(pwd)"
```

## Existing Code

Before we start hacking away at things, how will these steps fit into the
existing architecture of the project? Where is the `wasm-bindgen` dependency
installed, and will we need to either augment an existing check for this
dependency, or add a new checking function altogether?

### Step 4 - Installing `wasm-bindgen`

This step is a step in the `init` sub-command's process. You can find the
`step_install_wasm_bindgen` function (the prototype of which is shown below)
defined at `command::init.rs:186`.

This is the `Init` struct that these functions are implemented within.

```rust
pub struct Init {
  crate_path: String,
  scope: Option<String>,
  disable_dts: bool,
  target: String,
  debug: bool,
  crate_name: Option<String>,
}
```

Within the `process` method, which runs each of the steps for the given mode,
the method representing the step of installing `wasm-bingen` is invoked.

```rust
fn step_install_wasm_bindgen(
  &mut self,
  step: &Step,
  log: &Logger,
) -> result::Result<(), Error> {
  // ...
}
```

The `step_install_wasm_bindgen` method calls
`bindgen::cargo_install_wasm_bindgen(..)`, which is responsible for the details
of installing `wasm-bindgen`. This is found at `bindgen.rs:7`

The line(s) responsible for shelling out and installing the CLI crate looks
like this:

```rust
let output = Command::new("cargo")
  .arg("install")
  .arg("wasm-bindgen-cli")
  .arg("--force")
  .output()?;
```

Notably, this is where we are currently trying to figure out if the crate
is already installed. As is, it is considered to already exist if this
command fails. Luckily, I did already have a branch from a previously existing
PR that implemented some of the logic concerning what this check should look
like. The details will change because of how much the codebase has changed
over the past few versions, but this should work very well as a general
starting point to build off of.

