# Tech Debt Backlog

This backlog captures follow-up improvements discovered during the C++ to Rust migration work.

## Entry template

- **Title**:
- **Impacted files/modules**:
- **Risk/impact**:
- **Suggested follow-up**:
- **Priority**: low | medium | high

## Current backlog

- **Completed**: Harden cross-platform determinism for `sex` and `shaded_green_beard_instructions`
- **Impacted files/modules**: `avida-core/tests/sex/config/avida.cfg`, `avida-core/tests/sex/config/events.cfg`, `avida-core/tests/shaded_green_beard_instructions/config/avida.cfg`, `avida-core/tests/shaded_green_beard_instructions/config/eventsFullPopTest.cfg`, fixture `expected/data/*`, `.github/workflows/ci.yaml`
- **Result**: Pinned fixture-local determinism knobs, reduced high-variance assertion surface (dominant/average/time/archive outputs), and improved CI diagnostics with single-run non-blocking diagnostics, explicit diff threshold, XML report artifacts, and selected-test manifests.
- **Next candidate**: Extend shared CString/output-pointer helper adoption to remaining Rust FFI modules.

- **Completed**: Extract deterministic `cResourceHistory` update-entry selection/retrieval helpers behind a narrow C ABI seam
- **Impacted files/modules**: `avida-core/source/main/cResourceHistory.cc`, `avida-core/include/private/rust/running_stats_ffi.h`, `rust/avida-rust/src/resource_history_helpers.rs`, `avida-core/source/targets/unit-tests/main.cc`
- **Result**: Routed exact/nearest entry selection and index-safe default-zero value retrieval through Rust FFI helpers with boundary-focused Rust/C++ parity tests, while preserving C++ ownership, parsing, and mutation flows.
- **Next candidate**: Extend shared CString/output-pointer helper adoption to remaining Rust FFI modules.

- **Completed**: Re-stabilize cross-platform consistency fixtures for `sex` and `shaded_green_beard_instructions`
- **Impacted files/modules**: `avida-core/tests/sex/test_list`, `avida-core/tests/shaded_green_beard_instructions/test_list`, corresponding `expected/data/*.dat`
- **Result**: Re-enabled both consistency tests in default short runs by removing the `long` flag and verifying deterministic pass in `slave` mode for targeted runs.
- **Next candidate**: Extend shared CString/output-pointer helper adoption to remaining Rust FFI modules.

- **Completed**: Add Rust coverage gate with initial 75% line threshold in CI
- **Impacted files/modules**: `.github/workflows/ci.yaml`, `rust/avida-rust/scripts/ci_coverage_check.sh`, `rust/avida-rust` test targets
- **Result**: Added `coverage-rust` workflow lane using `cargo-llvm-cov` and a reproducible script gate (`./scripts/ci_coverage_check.sh 75`) with artifact upload for diagnostics. Initial scope gates migrated FFI-centric modules while legacy low-coverage modules are explicitly excluded by regex.
- **Next candidate**: Expand coverage gate scope to remaining Rust modules (`running_*`, weighted indexes, histogram, bit array), then tighten threshold gradually (80% -> 85%).

- **Completed**: Lock explicit boundary policy for `cResourceCount` scheduling helper math
- **Impacted files/modules**: `rust/avida-rust/src/resource_count_helpers.rs`, `avida-core/source/main/cResourceCount.cc`, `avida-core/source/targets/unit-tests/main.cc`
- **Result**: Added explicit truncation/saturation guards in Rust scheduling helpers plus boundary fixtures for negative, NaN/Inf, and zero/invalid-step behavior in Rust+C++ tests.
- **Next candidate**: Expand Wave 5 `cResourceCount` seam to deterministic spatial-update scheduling helpers.

- **Completed**: Extend shared CString/output-pointer helper adoption to remaining Rust FFI modules
- **Impacted files/modules**: `rust/avida-rust/src/common.rs`, `rust/avida-rust/src/resource_count_helpers.rs`, `rust/avida-rust/src/resource_history_helpers.rs`, `avida-core/source/targets/unit-tests/main.cc`
- **Result**: Consolidated remaining low-risk raw pointer/C-string handling through shared `common.rs` helpers (`with_cstr`, new `with_slice`) and locked null/invalid input parity with expanded Rust+C++ unit tests.
- **Next candidate**: Keep new FFI modules aligned with shared pointer-accessor macro and CString helper conventions using a reviewer checklist.

- **Title**: Keep new FFI modules aligned with shared pointer-accessor macro pattern
- **Impacted files/modules**: `rust/avida-rust/src/common.rs`, future `rust/avida-rust/src/*` FFI modules
- **Risk/impact**: The shared macro now removes duplicate pointer boilerplate for current handles, but new modules can regress if they reintroduce ad hoc null-check/deref wrappers.
- **Suggested follow-up**: Require new FFI handle wrappers to use `common.rs` macro-generated accessors and add a small reviewer checklist item for this rule.
- **Priority**: low

- **Title**: Expand phenotype grouping consistency fixture with additional sequence/task diversity
- **Impacted files/modules**: `avida-core/tests/analyze_printphenotypes_grouping/config/analyze.cfg`, `avida-core/tests/analyze_printphenotypes_grouping/expected/data/total_task_count`
- **Risk/impact**: Current fixture validates grouping/count semantics on a compact representative case, but does not yet cover broader reaction/task combinations.
- **Suggested follow-up**: Add 2-3 additional sequence families (including edge-case non-viable and high-task variants) while keeping expected output compact and deterministic.
- **Priority**: low

- **Title**: Audit CMake policy and option compatibility after 3.20 uplift
- **Impacted files/modules**: `CMakeLists.txt`, `avida-core/CMakeLists.txt`, `build_avida`, vendor `CMakeLists.txt`
- **Risk/impact**: Version uplift is complete, but latent policy/option assumptions can still fail on fresh toolchains or cache reuse.
- **Suggested follow-up**: Add a clean-configure CI step that validates first-pass and reconfigure behavior with CMake 3.20+.
- **Priority**: low

- **Title**: Harden CMake reconfigure behavior for vendored `backward-cpp`
- **Impacted files/modules**: `libs/backward-cpp/CMakeLists.txt`, top-level configure flow
- **Risk/impact**: In-place reconfigure can hit `Backward::Backward` alias redefinition when stale cache state persists, causing first-pass configure failures.
- **Suggested follow-up**: Guard alias creation or avoid repeated vendor configure state in existing build trees.
- **Priority**: low

- **Title**: Expand manager/provider cross-module classify matrix into consistency fixtures
- **Impacted files/modules**: `avida-core/source/targets/unit-tests/main.cc`, `avida-core/tests/*`, `rust/avida-rust/src/provider_helpers.rs`
- **Risk/impact**: Unit-level classify/dispatch matrix now covers standard/argumented/malformed ids, but end-to-end consistency fixtures do not yet stress broader id-shape variation through full run workflows.
- **Suggested follow-up**: Add a compact consistency fixture set that exercises additional malformed and nested-bracket-like id forms across provider/manager runtime dispatch paths.
- **Priority**: low

- **Completed**: Expand Wave 5 `cResourceCount` seam to deterministic spatial-update scheduling helpers
- **Impacted files/modules**: `avida-core/source/main/cResourceCount.cc`, `rust/avida-rust/src/resource_count_helpers.rs`, `avida-core/include/private/rust/running_stats_ffi.h`, `avida-core/source/targets/unit-tests/main.cc`
- **Result**: Routed `m_spatial_update - m_last_updated` derivation through new Rust helper `avd_rc_num_spatial_updates` with explicit saturating boundary behavior and dual-language parity fixtures, while leaving spatial mutation/state transitions in C++.
- **Next candidate**: Extend shared CString/output-pointer helper adoption to remaining Rust FFI modules.

- **Title**: Broaden `Data::Package` primitive formatting parity matrix coverage
- **Impacted files/modules**: `rust/avida-rust/src/package.rs`, `avida-core/source/targets/unit-tests/main.cc`, `libs/apto/include/apto/core/StringUtils.h`
- **Risk/impact**: Primitive `Wrap<T>::StringValue` is now Rust-backed, but only a compact set of `%g` edge cases is currently locked by tests; rare legacy formatting edge values may regress silently.
- **Suggested follow-up**: Add a larger shared bool/int/double fixture matrix (boundary integers, denormals, exponent thresholds, signed zero) and verify Rust/C++ output equivalence against `Apto::AsStr`.
- **Priority**: low

- **Title**: Harden `Data::TimeSeriesRecorder` typed parse policy parity across C++ and Rust
- **Impacted files/modules**: `rust/avida-rust/src/time_series_recorder.rs`, `avida-core/source/data/TimeSeriesRecorder.cc`
- **Risk/impact**: Typed getter parsing now rejects malformed entries safely, but accepted text forms (especially for bool/string edge forms) may still diverge from historic `Apto::StrAs` behavior in obscure inputs.
- **Suggested follow-up**: Add focused parity tests for legacy string forms and decide whether to tighten or exactly emulate prior coercion rules.
- **Priority**: low

- **Title**: Evaluate selective `bitvec` production adoption after parity prototype
- **Impacted files/modules**: `rust/avida-rust/src/bit_array.rs`, `rust/avida-rust/Cargo.toml`
- **Risk/impact**: A `bitvec` parity prototype now exists in tests, but production code still uses custom bit-field logic; adoption without targeted profiling could add complexity without runtime gains.
- **Suggested follow-up**: Benchmark critical bit operations and adopt `bitvec` in production only where measured maintainability/performance wins are clear.
- **Priority**: low

- **Title**: Add broader manager/provider cross-module ID semantics matrix tests
- **Impacted files/modules**: `avida-core/source/targets/unit-tests/main.cc`, `avida-core/source/data/Manager.cc`, `avida-core/source/data/Provider.cc`
- **Risk/impact**: Manager now routes major classification/split paths through shared Rust helpers, but regression risk remains for less common ID shapes unless checked across both modules with one parity matrix.
- **Suggested follow-up**: Add a compact shared test matrix (standard/argumented/malformed variants) exercised through both Manager and Provider-facing paths.
- **Priority**: low

