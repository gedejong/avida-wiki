# Wave 5 Starter Seam: `cResourceCount` Deterministic Lookup Helpers

## Goal

Define a low-risk first Wave 5 migration slice that extracts deterministic resource-name lookup/classification helpers from `cResourceCount` into Rust, without moving update-loop state evolution.

## Why this seam first

- It isolates pure helper logic from simulation-time side effects.
- It creates a reusable C ABI pattern for future `source/main` slices.
- It avoids ownership changes in `cWorld`, `cPopulation`, and spatial resource state.

## Initial scope

- In scope:
  - Name-to-index lookup helper behavior currently in `GetResourceCountID` and duplicate `GetResourceByName`.
  - Deterministic malformed-name handling (`-1`/not found semantics).
  - Focused parity tests for duplicate-name, missing-name, and empty-name paths.
- Out of scope:
  - `DoUpdates`, `DoSpatialUpdates`, `DoNonSpatialUpdates`, and any update-time resource flow math.
  - Resource storage ownership/layout changes.
  - Concurrency or lock model changes.

## Planned files (starter slice)

- C++ call-site integration:
  - `avida-core/source/main/cResourceCount.cc`
  - `avida-core/source/main/cResourceCount.h` (only if declaration updates are required)
- C ABI:
  - `avida-core/include/private/rust/running_stats_ffi.h`
- Rust implementation:
  - `rust/avida-rust/src/resource_count_helpers.rs` (new module candidate)
  - `rust/avida-rust/src/lib.rs` (module export/wiring)
- Tests:
  - `avida-core/source/targets/unit-tests/main.cc`
  - `rust/avida-rust/src/resource_count_helpers.rs` (unit tests)

## Suggested first API sketch

- `int avd_rc_lookup_resource_index(const char* const* names, int count, const char* query);`

Behavior target:

- Return first matching index on exact match.
- Return `-1` when not found, null input, or malformed pointers.
- Preserve existing C++ visible behavior for current call sites.

## Validation gates for execution slice

- `cd rust/avida-rust && cargo test --all-targets`
- `cd rust/avida-rust && cargo fmt --check`
- `cd rust/avida-rust && cargo clippy --all-targets -- -D warnings`
- `./build_avida -DAVD_UNIT_TESTS:BOOL=ON -DAPTO_UNIT_TESTS:BOOL=OFF`
- `./cbuild/work/unit-tests`
- `./run_tests --mode=slave`
