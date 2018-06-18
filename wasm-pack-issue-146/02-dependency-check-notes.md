# Dependency Check Notes

The `Cargo.toml` file is already checked for a `wasm-bindgen` dependency.
We need to add some functionality, by adding a function that will return
a String containing the version that we are depending on.

As is, the check function is defined at `manifest.rs:159`.

