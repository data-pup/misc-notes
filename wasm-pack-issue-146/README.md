# `wasm-pack` Issue 146 Notes

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

