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

The `01-install-process-notes.md` file contains some notes on the
`wasm-bindgen` installation process.

