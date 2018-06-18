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

