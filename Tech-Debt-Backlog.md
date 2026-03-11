# Tech Debt Backlog

This backlog captures follow-up improvements discovered during the C++ to Rust migration work.

## Entry template

- **Title**:
- **Impacted files/modules**:
- **Risk/impact**:
- **Suggested follow-up**:
- **Priority**: low | medium | high

## Current backlog

- **Completed**: Evaluate selective `bitvec` production adoption for `cBitArray` internals
- **Impacted files/modules**: `rust/avida-rust/src/bit_array.rs`, `rust/avida-rust/benches/critical_paths.rs`, `rust/avida-rust/Cargo.toml`
- **Result**: Added focused Criterion benchmark coverage for representative `shift`/`increment`/`count` workloads and expanded Rust parity matrices across bitwise ops/width edges; results were mixed to marginal, so production bit-array internals remain on the existing custom bit-field implementation (no ABI changes) while retaining stronger benchmark/parity evidence.
- **Next candidate**: Add a dedicated CI matrix leg for explicit backtrace-enabled builds (`AVIDA_ENABLE_BACKTRACE=1`) to keep optional diagnostics paths continuously tested.

- **Completed**: Harden CMake reconfigure robustness and default build log signal-to-noise
- **Impacted files/modules**: `CMakeLists.txt`, `build_avida`, `.github/workflows/ci.yaml`, `avida-core/CMakeLists.txt`
- **Result**: Locked low-noise default configure behavior (backtrace path opt-in), added CI configure/reconfigure smoke check in one build tree, and preserved Linux static link-order correctness for `aptostatic` resolution.
- **Next candidate**: Add a dedicated CI matrix leg for explicit backtrace-enabled builds (`AVIDA_ENABLE_BACKTRACE=1`) to keep optional diagnostics path continuously tested without polluting default logs.

- **Completed**: Broaden `Data::Package` primitive formatting parity matrix coverage
- **Impacted files/modules**: `rust/avida-rust/src/package.rs`, `avida-core/source/targets/unit-tests/main.cc`, `libs/apto/include/apto/core/StringUtils.h`
- **Result**: Expanded shared Rust/C++ parity matrices for `Wrap<bool/int/double>::StringValue` across boundary integers, signed zero, denormals, `%g` threshold cutovers, and NaN/Inf, keeping existing FFI surface unchanged while locking `Apto::AsStr` parity.
- **Next candidate**: Evaluate selective `bitvec` production adoption for `bit_array` where benchmarked wins justify added dependency/complexity.

- **Completed**: Harden `Data::TimeSeriesRecorder` typed getter parse-policy parity with legacy `Apto::StrAs`
- **Impacted files/modules**: `rust/avida-rust/src/time_series_recorder.rs`, `avida-core/source/targets/unit-tests/main.cc`, `avida-core/source/data/TimeSeriesRecorder.cc`, `libs/apto/include/apto/core/StringUtils.h`
- **Result**: Updated Rust typed coercion to legacy semantics (`bool` accepts only exact true aliases, `int`/`double` use C-style coercion with partial parses) and locked behavior via shared Rust/C++ edge-case matrix fixtures, without ABI expansion.
- **Next candidate**: Evaluate selective `bitvec` production adoption for `bit_array` where benchmarked wins justify added dependency/complexity.

- **Completed**: Extract deterministic `cEventList` trigger/timing parsing behind additive Rust helpers
- **Impacted files/modules**: `avida-core/source/main/cEventList.cc`, `avida-core/include/private/rust/running_stats_ffi.h`, `rust/avida-rust/src/event_list_helpers.rs`, `rust/avida-rust/src/lib.rs`, `avida-core/source/targets/unit-tests/main.cc`
- **Result**: Routed trigger alias classification and timing tuple decoding (`start`, `start:interval`, `start:interval:stop`, including `begin`/`all`/`once`/`end`) through Rust C ABI helpers with null/invalid guards and dual-language parity fixtures, while keeping event creation and state mutation in C++.
- **Next candidate**: Lock legacy text-coercion parity for remaining parser seams (`Data::Package` and `Data::TimeSeriesRecorder`) with shared Rust/C++ matrix fixtures.

- **Completed**: Extract deterministic `cResourceCount::Setup` precalc table derivation behind additive Rust helper
- **Impacted files/modules**: `avida-core/source/main/cResourceCount.cc`, `avida-core/include/private/rust/running_stats_ffi.h`, `rust/avida-rust/src/resource_count_helpers.rs`, `rust/avida-rust/src/common.rs`, `avida-core/source/targets/unit-tests/main.cc`
- **Result**: Routed setup-time recurrence table generation (`decay_precalc`/`inflow_precalc`) through new Rust FFI helper with parity-checked outputs and null/invalid guard fixtures, while preserving C++ ownership and runtime update behavior.
- **Next candidate**: Keep new FFI modules aligned with shared pointer-accessor macro and CString helper conventions using a reviewer checklist.

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

- **Title**: Add explicit CI backtrace-enabled validation leg (opt-in path)
- **Impacted files/modules**: `.github/workflows/ci.yaml`, `build_avida`, `CMakeLists.txt`
- **Risk/impact**: Default CI path is now intentionally low-noise with backtrace disabled unless opted in; regressions in optional backtrace plumbing could go undetected.
- **Suggested follow-up**: Add a targeted CI leg that sets `AVIDA_ENABLE_BACKTRACE=1` and runs configure/build smoke checks so optional diagnostics remain healthy.
- **Priority**: low

- **Completed**: Expand manager/provider cross-module ID classify matrix into consistency fixtures
- **Impacted files/modules**: `avida-core/source/data/Manager.cc`, `avida-core/source/data/Provider.cc`, `rust/avida-rust/src/provider_helpers.rs`, `avida-core/source/targets/unit-tests/main.cc`, `avida-core/tests/manager_provider_id_dispatch/*`
- **Result**: Hardened manager/provider dispatch around existing `avd_provider_classify_id` seam, expanded Rust+C++ edge-shape matrices (standard/argumented/malformed/nested-bracket-like IDs), and added a focused consistency fixture (`manager_provider_id_dispatch`) that exercises runtime data-id dispatch flow.
- **Next candidate**: Evaluate selective `bitvec` production adoption for `bit_array` where benchmarked wins justify added dependency/complexity.

- **Completed**: Expand Wave 5 `cResourceCount` seam to deterministic spatial-update scheduling helpers
- **Impacted files/modules**: `avida-core/source/main/cResourceCount.cc`, `rust/avida-rust/src/resource_count_helpers.rs`, `avida-core/include/private/rust/running_stats_ffi.h`, `avida-core/source/targets/unit-tests/main.cc`
- **Result**: Routed `m_spatial_update - m_last_updated` derivation through new Rust helper `avd_rc_num_spatial_updates` with explicit saturating boundary behavior and dual-language parity fixtures, while leaving spatial mutation/state transitions in C++.
- **Next candidate**: Extend shared CString/output-pointer helper adoption to remaining Rust FFI modules.

