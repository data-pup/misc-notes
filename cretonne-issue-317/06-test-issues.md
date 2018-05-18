After I fixed up some of the issues mentioned in the previous document,
registered my recipe, and fixed up the predicate required, I ran into some
issues in the test bench. This was the output I saw when I ran `cargo test`.

```
running 0 tests

test result: ok. 0 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out


running 1 test
test filetests ... FAILED

failures:

---- filetests stdout ----
	FAIL filetests/wasm/conversions.cton: panicked in worker #2: assertion failed: `(left == right)`
  left: `65`,
 right: `62`
FAIL filetests/wasm/f64-arith.cton: compile(%f64_abs): Expected code size 25, got 18
FAIL filetests/wasm/f32-arith.cton: compile(%f32_abs): Expected code size 21, got 18
FAIL filetests/isa/x86/optimized-zero-constants.cton: binemit(%foo): No encodings found for: v0 = f32const 0.0
slow: 0.753 filetests/preopt/rem_by_const_non_power_of_2.cton
slow: 0.538 filetests/preopt/div_by_const_non_power_of_2.cton
slow: 0.532 filetests/preopt/rem_by_const_power_of_2.cton
slow: 0.630 filetests/parser/tiny.cton
slow: 0.665 filetests/regalloc/coalescing-207.cton
slow: 0.546 filetests/isa/x86/prologue-epilogue.cton
122 tests
thread 'filetests' panicked at 'test harness: "4 failures"', libcore/result.rs:945:5


failures:
    filetests

test result: FAILED. 0 passed; 1 failed; 0 ignored; 0 measured; 0 filtered out
```

