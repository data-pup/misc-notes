# `wasm-pack` Issue 146 Notes

This folder contains notes on work to solve an issue for `wasm-pack`. Before
continuing further into the issue description, and notes about the
implementation of a solution, I will note that I began my work starting
from this commit hash, in case you would like to follow along:
`1c50b12b8fa4ca1984c711336a5ae11134682844`.

### Issue Description

```
currently we are force installing wasm-bindgen, but this is frustrating because
it takes a long time and often doesn't need to happen. i'd love a solution for
this. let's use this issue to brainstorm. (related to #51 )

strawman proposal:

read cargo.toml to find wasm-bindgen lib version
check currently install wasm-bindgen version
if not installed, install version listed in cargo.toml
  if installed, check version, if version mismatch install otherwise skip
```

### Implementation Strategy

Some notes regarding how to coordinate this was given by two of the other
members of the RustWasm team.

```
Would it be possible to compile a version of wasm-bindgen-cli in say the
target directory and use the one there? That way it's not checked in by
accident and we can have it be project dependent for wasm-bindgen-cli.

- @mgattozzi
```

```
What I did in the template was to ensure that a project-local wasm-bindgen CLI
was always installed by using cargo install --root like this:
https://github.com/rustwasm/rust_wasm_template/blob/master/ci/script.sh#L155-L174

- @fitzgen
```

### Template CI Script Example

The shell script section mentioned in the comments above provide an example
of how to check that the correct version of the `wasm-bindgen` dependency
has been installed locally within the project.

```sh
function ensure_wasm_bindgen_installed {
  local version=$(get_wasm_bindgen_version)
  local version_string="wasm-bindgen $version"

  if test -x ./bin/wasm-bindgen; then
    if test "$(./bin/wasm-bindgen --version | xargs)" == "$version_string"; then
      echo 'Correct version of wasm-bindgen already installed locally.'
      return
    fi

    echo "Wrong version installed locally, updating to $version."
  else
    echo 'wasm-bindgen not installed locally, installing.'
  fi

  logged cargo \
          cargo +nightly install -f wasm-bindgen-cli \
          --version "$version" \
          --root "$(pwd)"
}
```

What is happening here? There a calls to a few functions that are defined
elsewhere in the script, so let's summarize the actions that will need to be
taken.

1.  Creating a string containing the expected version of `wasm_bindgen`, by
    finding the row defining that dependency in the `Cargo.toml` file.
2.  Check if `wasm_bindgen` is installed locally by looking for it relatively
    at `./bin/wasm-bindgen` starting from the project root directory. If it
    does not exist there, break and install `wasm-bindgen-cli` locally.
3.  If `wasm_bindgen` _is_ in the `$PATH`, check that the version is correct.
    Note that there may be multiple versions of this installed, so we should
    check that _one_ of these versions matches the version specified in the
    `Cargo.toml` file's dependencies.

The general overview of this logic should look nearly identical within
`wasm-pack`, but you know, using Rust rather than shell.

