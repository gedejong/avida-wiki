# Tech Debt Backlog - Completed Archive

Archived on 2026-03-11 from `documentation/Tech-Debt-Backlog.md`.

## Completed Entries

- **Completed**: Extract deterministic `cSpatialResCount::SetPointers` neighbor-link derivation/masking helper behind additive Rust FFI
- **Impacted files/modules**: `avida-core/source/main/cSpatialResCount.cc`, `avida-core/include/private/rust/running_stats_ffi.h`, `rust/avida-rust/src/spatial_res_count_helpers.rs`, `avida-core/source/targets/unit-tests/main.cc`
- **Result**: Routed per-slot neighbor entry derivation (`elempt`, `xdist`, `ydist`, `dist`) through `avd_src_setpointer_entry`, preserving C++ pointer ownership/traversal and exact GRID masking behavior (`cResource::NONE`) with Rust+C++ parity matrix tests across corners/edges/interior plus degenerate dimensions (`1x1`, `1xN`, `Nx1`).
- **Next candidate**: Extract deterministic `cSpatialResCount::FlowAll` per-neighbor transfer accumulation helper math behind additive Rust FFI while preserving C++ grid traversal and state mutation ownership.

- **Completed**: Extract deterministic `cSpatialResCount` cell-list bounds/valid-id decision helpers behind additive Rust FFI
- **Impacted files/modules**: `avida-core/source/main/cSpatialResCount.cc`, `avida-core/include/private/rust/running_stats_ffi.h`, `rust/avida-rust/src/spatial_res_count_helpers.rs`, `avida-core/source/targets/unit-tests/main.cc`
- **Result**: Routed duplicated cell-id bounds checks through Rust helper policies for strict (`< size`) and legacy SetCellList (`<= size`) semantics, preserving C++ traversal/state ownership while locking policy boundaries with Rust+C++ parity tests.
- **Next candidate**: Extract deterministic `cSpatialResCount::SetPointers` neighbor-link row derivation/masking helper math behind additive Rust FFI while preserving C++ pointer ownership and traversal loops.

- **Completed**: Extract deterministic `cSpatialResCount` wrapped rectangle index helper behind additive Rust FFI
- **Impacted files/modules**: `avida-core/source/main/cSpatialResCount.cc`, `avida-core/include/private/rust/running_stats_ffi.h`, `rust/avida-rust/src/spatial_res_count_helpers.rs`, `avida-core/source/targets/unit-tests/main.cc`
- **Result**: Routed wrapped rectangle coordinate-to-element index mapping from duplicated inline `Mod(...)` math in `Source`/`Sink` through `avd_src_wrapped_elem_index`, preserving C++ loop/mutation ownership while locking wrap/invalid-dimension behavior with Rust+C++ parity matrices.
- **Next candidate**: Extract deterministic `cSpatialResCount` cell-list bounds/valid-id decision helper math behind additive Rust FFI while preserving C++ traversal/state ownership.

- **Completed**: Expand Rust coverage gate scope and raise threshold incrementally
- **Impacted files/modules**: `rust/avida-rust/scripts/ci_coverage_check.sh`, `.github/workflows/ci.yaml`
- **Result**: Coverage gate now emits a persisted summary artifact, validates representative stable-module rows to catch scope regressions, and raises CI line-coverage threshold from 80% to 82% while preserving deterministic pass.
- **Next candidate**: Extract deterministic `cSpatialResCount` wrapped rectangle-to-element index helper math behind additive Rust FFI while preserving C++ traversal/state ownership.

- **Completed**: Provider/history deterministic readability hardening (no ABI drift)
- **Impacted files/modules**: `rust/avida-rust/src/provider_helpers.rs`, `rust/avida-rust/src/resource_history_helpers.rs`
- **Result**: Restructured provider ID parsing internals into a typed parse result and simplified nearest-entry scan readability in resource-history helpers, preserving C ABI and return semantics with added duplicate-selection parity coverage.
- **Next candidate**: Expand Rust coverage gate scope in `rust/avida-rust/scripts/ci_coverage_check.sh` and increment threshold with passing evidence.

- **Completed**: TimeSeries FFI guardrail cleanup using shared handle/output helpers
- **Impacted files/modules**: `rust/avida-rust/src/time_series_recorder.rs`, `rust/avida-rust/src/common.rs`
- **Result**: Consolidated pointer/null/output handling through shared `common.rs` accessors for typed/string getter paths, preserving existing ABI and legacy coercion behavior while locking null-handle and out-param non-mutation semantics with explicit tests.
- **Next candidate**: Apply deterministic readability hardening in `provider_helpers` and `resource_history_helpers` internals while preserving exported ABI and parity behavior.

- **Completed**: Extract deterministic `cSpatialResCount` source/sink scalar helpers behind additive Rust FFI
- **Impacted files/modules**: `avida-core/source/main/cSpatialResCount.cc`, `avida-core/include/private/rust/running_stats_ffi.h`, `rust/avida-rust/src/spatial_res_count_helpers.rs`, `avida-core/source/targets/unit-tests/main.cc`
- **Result**: Routed source per-cell distribution and clamped sink/cell-outflow delta math through new Rust helpers (`avd_src_source_per_cell`, `avd_src_sink_delta`, `avd_src_cell_outflow_delta`) while keeping C++ grid traversal/indexing and `Rate(...)` state ownership unchanged, with Rust+C++ parity fixtures for scalar behavior and guard semantics.
- **Next candidate**: Extract deterministic `cSpatialResCount` wrapped rectangle-to-element index helper math behind additive Rust FFI while preserving C++ loop/state ownership.

- **Completed**: Extract deterministic `cSpatialResCount` flow-scalar and range-normalization helpers behind additive Rust FFI
- **Impacted files/modules**: `avida-core/source/main/cSpatialResCount.cc`, `avida-core/source/main/cResourceCount.cc`, `avida-core/include/private/rust/running_stats_ffi.h`, `rust/avida-rust/src/spatial_res_count_helpers.rs`, `rust/avida-rust/src/lib.rs`, `avida-core/source/targets/unit-tests/main.cc`
- **Result**: Routed `CheckRanges` span normalization and `FlowMatter` scalar computation through additive Rust helpers with boundary/legacy-guard parity fixtures, while keeping C++ grid traversal and state mutation ownership unchanged.
- **Next candidate**: Add lightweight reviewer checklist + guardrails to keep new Rust FFI modules aligned with shared `common.rs` pointer-accessor and CString/output-pointer helper conventions.

- **Completed**: Extract deterministic `cResourceCount` non-spatial step-application kernel behind additive Rust helper
- **Impacted files/modules**: `avida-core/source/main/cResourceCount.cc`, `avida-core/include/private/rust/running_stats_ffi.h`, `rust/avida-rust/src/resource_count_helpers.rs`, `avida-core/source/targets/unit-tests/main.cc`
- **Result**: Routed `DoNonSpatialUpdates` chunked recurrence application (`while num_steps > PRECALC_DISTANCE` + remainder application) through new Rust helper `avd_rc_apply_nonspatial_steps`, preserving C++ resource ownership/state while centralizing deterministic update math with Rust+C++ parity and invalid-input guard fixtures.
- **Next candidate**: Add lightweight reviewer checklist + guardrails to keep new Rust FFI modules aligned with shared `common.rs` pointer-accessor and CString/output-pointer helper conventions.

- **Completed**: Add explicit backtrace-enabled CI validation leg (opt-in path)
- **Impacted files/modules**: `.github/workflows/ci.yaml`, `build_avida`, `CMakeLists.txt`, `libs/backward-cpp/CMakeLists.txt`
- **Result**: Added a dedicated CI smoke job that explicitly sets `AVIDA_ENABLE_BACKTRACE=1` for configure/reconfigure + build coverage while preserving default low-noise lanes; also hardened vendored `backward-cpp` alias creation to avoid duplicate-target failures when backtrace mode is enabled.
- **Next candidate**: Add reviewer checklist + lightweight guardrails to keep new Rust FFI modules aligned with shared `common.rs` pointer-accessor and CString/output-pointer helper conventions.

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

- **Completed**: Expand manager/provider cross-module ID classify matrix into consistency fixtures
- **Impacted files/modules**: `avida-core/source/data/Manager.cc`, `avida-core/source/data/Provider.cc`, `rust/avida-rust/src/provider_helpers.rs`, `avida-core/source/targets/unit-tests/main.cc`, `avida-core/tests/manager_provider_id_dispatch/*`
- **Result**: Hardened manager/provider dispatch around existing `avd_provider_classify_id` seam, expanded Rust+C++ edge-shape matrices (standard/argumented/malformed/nested-bracket-like IDs), and added a focused consistency fixture (`manager_provider_id_dispatch`) that exercises runtime data-id dispatch flow.
- **Next candidate**: Evaluate selective `bitvec` production adoption for `bit_array` where benchmarked wins justify added dependency/complexity.

- **Completed**: Expand Wave 5 `cResourceCount` seam to deterministic spatial-update scheduling helpers
- **Impacted files/modules**: `avida-core/source/main/cResourceCount.cc`, `rust/avida-rust/src/resource_count_helpers.rs`, `avida-core/include/private/rust/running_stats_ffi.h`, `avida-core/source/targets/unit-tests/main.cc`
- **Result**: Routed `m_spatial_update - m_last_updated` derivation through new Rust helper `avd_rc_num_spatial_updates` with explicit saturating boundary behavior and dual-language parity fixtures, while leaving spatial mutation/state transitions in C++.
- **Next candidate**: Extend shared CString/output-pointer helper adoption to remaining Rust FFI modules.

### Moved from the backlog on 2026-11-15 (FFI migration records and resolved decisions)

- **Title**: Open decision (maintainer): does the native port supersede the FFI migration in `rust/avida-rust`?
- **Resolved 2026-11-12**: the maintainer ruled that the native port supersedes the FFI migration. `rust/avida-rust` and the C++ core were removed; the tag `cpp-final` is the last commit that has them. The text below is kept as the record of the question.
- **Impacted files/modules**: `rust/avida-rust/`, `avida-core/source/cpu/cHardwareCPU.cc`, `avida-core/source/cpu/cHardwareFFI.cc`, `rust-migration-*.md`, `CLAUDE.md` (migration state and PO priorities)
- **Risk/impact**: The FFI track (807 `avd_*` exports, 372/518 CPU handlers delegating, roughly 28K lines of Rust) and the native track solve the same problem in two ways. If both continue, effort on the remaining 146 handlers and on `Divide_Main`/`Allocate_Main` ownership (FFI Wave 14) duplicates work the native `hardware` and `world` crates already contain. If the native port is accepted, that FFI work becomes moot; if it is not, the native workspace needs an owner.
- **Suggested follow-up**: Maintainer decides. Until then, treat the FFI "Next candidate" entries in `rust-migration-waves.md` (mating-type and `Inst_Repro` handlers, then `Inst_Collect*`) as paused rather than cancelled, and do not start new FFI slices. `CLAUDE.md` still lists those FFI priorities; it was not edited as part of this documentation update.
- **Priority**: high

- **Title**: Open decision (maintainer): commit strategy for the untracked workspace
- **Resolved 2026-11-12**: the workspace was committed on the branch `native-rust-port` and pushed to `gedejong/avida-rs` on 2026-11-13. The text below is kept as the record of the question.
- **Impacted files/modules**: `rust/avida-native/` (including `Cargo.lock`, `docs/ontology.json`, `docs/test_matrix.json`, `target/`), `.gitignore`
- **Risk/impact**: The whole workspace exists only in the working tree. A lost working directory loses all of it. A single commit of 189K lines is hard to review; `target/` is excluded by `.gitignore`.
- **Suggested follow-up**: Decide between one import commit per crate in layer order (`apto` to `world`, then `analyze`, `script`, `avida`, then `session`, `viewmodel`, `gui`, `tui`) and a single import commit with the architecture documents first. `target/` is already ignored (`.gitignore` line 124). Stage files explicitly, as the slice workflow requires, and avoid unrelated submodule pointer changes.
- **Priority**: high

- **Title**: Open decision (maintainer): retire `rust/avida-rust` and the C++ once the port is accepted
- **Resolved 2026-11-12**: the maintainer had `rust/avida-rust` and the ported C++ removed; the tag `cpp-final` keeps them and the reference binary is built from it.
- **Impacted files/modules**: `rust/avida-rust/`, `avida-core/`, `libs/apto/`, `cbuild/`, `build_avida`, `run_tests`, `abi/avd_symbols_baseline.txt`
- **Risk/impact**: The C++ binary is the behavioural reference for every validation above, and it is built from the C++/FFI hybrid. Removing either before acceptance removes the oracle. The retirement policy in `rust-migration-execution-playbook.md` (stabilised through CI, no remaining call sites, isolated reviewable diff) was written for the FFI track and needs a native-port reading.
- **Suggested follow-up**: Define acceptance criteria first (for example zero DIFF on the suite for a stated number of weeks, the feature gaps below closed or accepted, human review of the front ends). Then archive rather than delete: keep a tag of the last hybrid commit, and keep a frozen reference binary for the consistency suite.
- **Priority**: medium

- **Title**: Human review of the GUI and TUI in real windows and terminals
- **Resolved 2026-11-14**: the maintainer judged the desktop app in a real window ("the window looks good") and the terminal interface in a real terminal ("tui looks good. No obvious bugs so far").
- **Impacted files/modules**: `rust/avida-native/crates/gui`, `rust/avida-native/crates/tui`, `rust/avida-native/crates/viewmodel`
- **Risk/impact**: Both front ends have unit and snapshot tests (`gui` 104, `tui` 105, `viewmodel` 28 `#[test]` attributes) but those cannot judge layout, legibility, input handling, resize behaviour or terminal compatibility. No human has reviewed them in a real window or terminal.
- **Suggested follow-up**: A maintainer session covering: egui at several window sizes and display scales, ratatui in at least two terminal emulators and over ssh, a long run for responsiveness of the session thread, and the pause and step controls against the `Stepper`. Record findings as separate entries.
- **Priority**: medium

- **Completed**: Extract deterministic `cResourceCount` gradient configuration assignment policy (`SetGradientCount` setter sequencing) behind additive Rust FFI
- **Impacted files/modules**: `avida-core/source/main/cResourceCount.cc`, `avida-core/include/private/rust/running_stats_ffi.h`, `rust/avida-rust/src/resource_count_helpers.rs`, `avida-core/source/targets/unit-tests/main.cc`
- **Result**: Routed `SetGradientCount` setter-order policy through new Rust helpers (`avd_rc_gradient_setter_count`, `avd_rc_gradient_setter_opcode`) while keeping C++ ownership/mutation sites intact and preserving final `ResetGradRes(ctx, worldx, worldy)` sequencing; added Rust and C++ parity/guard tests for sequence count/order mapping.
- **Next candidate**: Extract deterministic `cResourceCount` gradient variable inflow setter call policy (`SetGradPlatVarInflow`) behind additive Rust FFI while preserving C++ ownership, context threading, and direct setter semantics.

- **Completed**: Extract deterministic `cResourceCount` gradient scalar setter call policy behind additive Rust FFI
- **Impacted files/modules**: `avida-core/source/main/cResourceCount.cc`, `avida-core/include/private/rust/running_stats_ffi.h`, `rust/avida-rust/src/resource_count_helpers.rs`, `avida-core/source/targets/unit-tests/main.cc`
- **Result**: Routed scalar gradient setter wrappers (`SetGradientPlatInflow`, `SetGradientPlatOutflow`, `SetGradientConeInflow`, `SetGradientConeOutflow`, `SetGradientInflow`) through new Rust opcode policy helpers (`avd_rc_gradient_scalar_setter_count`, `avd_rc_gradient_scalar_setter_opcode`) while preserving C++ assertions, direct setter calls, and ownership; added Rust and C++ sequence/order/guard parity coverage.
- **Next candidate**: Extract deterministic `cResourceCount` gradient variable inflow setter call policy (`SetGradPlatVarInflow`) behind additive Rust FFI while preserving C++ ownership, context threading, and direct setter semantics.

- **Completed**: Extract deterministic `cResourceCount` write-path selection policy wiring behind additive Rust FFI
- **Impacted files/modules**: `avida-core/source/main/cResourceCount.cc`, `rust/avida-rust/src/resource_count_helpers.rs`, `avida-core/source/targets/unit-tests/main.cc`
- **Result**: Routed geometry-based write-path branch selection in `Set`/`ModifyCell` through Rust helper `avd_rc_is_spatial_geometry`, preserving C++ mutation sequencing, per-cell update side effects (`SetModified`), and ownership while locking helper policy behavior with refreshed C++ geometry/payload matrix checks.
- **Next candidate**: Extract deterministic `cResourceCount` gradient variable inflow setter call policy (`SetGradPlatVarInflow`) behind additive Rust FFI while preserving C++ ownership, context threading, and direct setter semantics.

- **Completed**: Extract deterministic `cResourceCount` `SetCellResources` write-path policy wiring behind additive Rust FFI
- **Impacted files/modules**: `avida-core/source/main/cResourceCount.cc`, `avida-core/include/private/rust/running_stats_ffi.h`, `rust/avida-rust/src/resource_count_helpers.rs`, `avida-core/source/targets/unit-tests/main.cc`
- **Result**: Routed `SetCellResources` geometry-based write-path selection through new Rust helper `avd_rc_setcell_write_path_kind`, preserving C++ traversal order, ownership, and intentional global/partial no-op behavior while locking mapping and payload-selection parity with refreshed Rust and C++ helper tests.
- **Next candidate**: Extract deterministic `cResourceCount` gradient variable inflow setter call policy (`SetGradPlatVarInflow`) behind additive Rust FFI while preserving C++ ownership, context threading, and direct setter semantics.

- **Completed**: Extract deterministic `cResourceCount` spatial resource initialization/setup/resize policy wiring behind additive Rust FFI
- **Impacted files/modules**: `avida-core/source/main/cResourceCount.cc`, `avida-core/include/private/rust/running_stats_ffi.h`, `rust/avida-rust/src/resource_count_helpers.rs`, `avida-core/source/targets/unit-tests/main.cc`
- **Result**: Routed setup-time geometry bucketing, spatial rectangle logging policy, and resize cell-count derivation through new Rust helpers (`avd_rc_setup_path_kind`, `avd_rc_should_log_spatial_rectangles`, `avd_rc_resize_cell_count`) while preserving C++ allocation ownership, traversal order, and legacy setup/resize semantics with refreshed Rust and C++ policy-matrix tests.
- **Next candidate**: Extract deterministic `cResourceCount` gradient variable inflow setter call policy (`SetGradPlatVarInflow`) behind additive Rust FFI while preserving C++ ownership, context threading, and direct setter semantics.

- **Completed**: Extract deterministic `cResourceCount` read-path selection policy wiring behind additive Rust FFI
- **Impacted files/modules**: `avida-core/source/main/cResourceCount.cc`, `rust/avida-rust/src/resource_count_helpers.rs`, `avida-core/source/targets/unit-tests/main.cc`
- **Result**: Routed geometry-based read-path branching for `cResourceCount` read APIs through Rust helper `avd_rc_read_path_kind` via `ReadCellResourceValue` and aggregate `Get` path, preserving `DoUpdates` sequencing/ownership while locking mapping and payload-selection parity with refreshed C++ helper tests.
- **Next candidate**: Extract deterministic `cResourceCount` gradient variable inflow setter call policy (`SetGradPlatVarInflow`) behind additive Rust FFI while preserving C++ ownership, context threading, and direct setter semantics.

- **Completed**: Extract deterministic `cSpatialResCount::FlowAll` per-neighbor transfer accumulation helper math behind additive Rust FFI
- **Impacted files/modules**: `avida-core/source/main/cSpatialResCount.cc`, `avida-core/include/private/rust/running_stats_ffi.h`, `rust/avida-rust/src/spatial_res_count_helpers.rs`, `avida-core/source/targets/unit-tests/main.cc`
- **Result**: Routed pairwise transfer accumulation to Rust helper `avd_src_compute_flow_pair_deltas` (including null-output guards), kept C++ traversal/update order ownership in `FlowAll`, and locked parity + conservation behavior with Rust/C++ helper test matrices.
- **Next candidate**: Extract deterministic `cSpatialResCount` aggregate-update helper math (`StateAll`/`SumAll`) behind additive Rust FFI while preserving C++ traversal/state ownership.

- **Completed**: Extract deterministic `cSpatialResCount` aggregate-update helper math (`StateAll`/`SumAll`) behind additive Rust FFI
- **Impacted files/modules**: `avida-core/source/main/cSpatialResCount.cc`, `avida-core/source/main/cSpatialCountElem.h`, `avida-core/include/private/rust/running_stats_ffi.h`, `rust/avida-rust/src/spatial_res_count_helpers.rs`, `avida-core/source/targets/unit-tests/main.cc`
- **Result**: Routed per-cell fold (`amount += delta; delta = 0`) and aggregate sum reduction through new Rust helpers (`avd_src_state_fold`, `avd_src_sum_amounts`) while preserving C++ ownership/order semantics and locking parity/guard behavior with Rust+C++ helper matrices.
- **Next candidate**: Extract deterministic `cSpatialResCount` bulk-rate/reset helper math (`RateAll`/`ResetResourceCounts`) behind additive Rust FFI while preserving C++ traversal and ownership.

- **Completed**: Extract deterministic `cSpatialResCount` bulk-rate/reset helper math (`RateAll`/`ResetResourceCounts`) behind additive Rust FFI
- **Impacted files/modules**: `avida-core/source/main/cSpatialResCount.cc`, `avida-core/include/private/rust/running_stats_ffi.h`, `rust/avida-rust/src/spatial_res_count_helpers.rs`, `avida-core/source/targets/unit-tests/main.cc`
- **Result**: Routed `RateAll` and `ResetResourceCounts` deterministic per-cell arithmetic through new Rust helpers (`avd_src_rate_next_delta`, `avd_src_reset_amount`) with explicit null-output guards, while preserving C++ loop ownership/order and fallback behavior; added Rust and C++ helper parity matrices covering positive/negative/zero cases.
- **Completed**: Extract deterministic `cSpatialResCount` cell-list bulk transform helper math (`SetCellList` initialization and bounded per-cell writes) behind additive Rust FFI
- **Impacted files/modules**: `avida-core/source/main/cSpatialResCount.cc`, `avida-core/include/private/rust/running_stats_ffi.h`, `rust/avida-rust/src/spatial_res_count_helpers.rs`, `avida-core/source/targets/unit-tests/main.cc`
- **Result**: Routed deterministic `SetCellList` per-cell initialization fold through new Rust helper `avd_src_setcell_apply_initial`, preserving C++ traversal/order and legacy bounds behavior (`avd_src_cell_id_in_bounds_legacy_setcell`) with explicit fallback parity and Rust/C++ guard tests.
- **Completed**: Extract deterministic `cResourceCount` precalc table-fill recurrence helpers (`SetInflow`/`SetDecay` table population loops) behind additive Rust FFI
- **Impacted files/modules**: `avida-core/source/main/cResourceCount.cc`, `avida-core/include/private/rust/running_stats_ffi.h`, `rust/avida-rust/src/resource_count_helpers.rs`, `avida-core/source/targets/unit-tests/main.cc`
- **Result**: Routed setter-time inflow/decay recurrence table population through new Rust helpers (`avd_rc_fill_inflow_precalc_table`, `avd_rc_fill_decay_precalc_table`) while preserving C++ setter sequencing (`id` guard + rate assignment first), row ownership, and recurrence seed semantics (`inflow[0]=0`, `decay[0]=1`) with Rust/C++ parity and guard coverage.
- **Completed**: Idiomatic Rust FFI hardening without ABI breaks
- **Impacted files/modules**: `rust/avida-rust/src/common.rs`, `rust/avida-rust/src/running_stats.rs`, `rust/avida-rust/src/running_average.rs`, `rust/avida-rust/src/double_sum.rs`, `rust/avida-rust/src/weighted_index.rs`, `rust/avida-rust/src/ordered_weighted_index.rs`, `rust/avida-rust/src/histogram.rs`, `rust/avida-rust/src/bit_array.rs`, `rust/avida-rust/src/provider_helpers.rs`, `rust/avida-rust/src/event_list_helpers.rs`, `rust/avida-rust/scripts/ci_ffi_analysis.py`, `rust/avida-rust/scripts/ci_abi_guard.py`, `.github/workflows/ci.yaml`, `rust/avida-rust/abi/avd_symbols_baseline.txt`
- **Result**: Reduced repeated unsafe lifecycle boilerplate by routing handle creation/free/clone patterns through shared helpers, tightened out-parameter writes to deterministic checked paths, and added a CI FFI analysis lane that publishes symbol/call-graph artifacts plus an ABI guard that fails only on removed `avd_*` exports.
- **Completed**: Idiomatic Rust FFI hardening phase 2 (ABI-safe)
- **Impacted files/modules**: `rust/avida-rust/src/common.rs`, `rust/avida-rust/src/bit_array.rs`, `rust/avida-rust/src/time_series_recorder.rs`, `rust/avida-rust/src/package.rs`, `rust/avida-rust/src/resource_count_helpers.rs`, `rust/avida-rust/scripts/ci_ffi_analysis.py`, `rust/avida-rust/scripts/ffi_error_outparam_policy.md`, `.github/workflows/ci.yaml`
- **Result**: Removed remaining pointer-deref allow annotations from active helper modules by routing binary/compare paths through shared accessors, centralized legacy bool/int/double coercion logic in `common.rs` for cross-module parity, standardized additional out-param guard behavior with focused tests, and added compact `ffi_summary.txt` CI artifact while keeping ABI guard policy unchanged (fail removals only).
- **Completed**: Extract deterministic `cResourceCount` spatial update-step dispatch helper math (per-update cell-list branch decision and step iteration policy) behind additive Rust FFI
- **Impacted files/modules**: `avida-core/source/main/cResourceCount.cc`, `avida-core/include/private/rust/running_stats_ffi.h`, `rust/avida-rust/src/resource_count_helpers.rs`, `avida-core/source/targets/unit-tests/main.cc`
- **Result**: Routed `DoSpatialUpdates` iteration-count normalization and cell-list branch decision through new Rust helpers (`avd_rc_spatial_step_iterations`, `avd_rc_use_cell_list_branch`) while preserving C++ state ownership and operation ordering; added Rust/C++ helper parity tests for positive/zero/negative boundaries and maintained additive ABI guard behavior.
- **Completed**: Extract deterministic `cResourceCount` per-resource update dispatch policy (global/non-spatial/spatial routing decision) behind additive Rust FFI
- **Impacted files/modules**: `avida-core/source/main/cResourceCount.cc`, `avida-core/include/private/rust/running_stats_ffi.h`, `rust/avida-rust/src/resource_count_helpers.rs`, `avida-core/source/targets/unit-tests/main.cc`
- **Result**: Routed `DoUpdates` geometry-based spatial classification, per-resource action decision, and `m_last_updated` advance policy through new Rust helpers (`avd_rc_is_spatial_geometry`, `avd_rc_dispatch_action`, `avd_rc_should_advance_last_updated`) while preserving C++ resource loop ordering, update scheduling behavior, and ownership/mutation sequencing; added Rust/C++ dispatch matrix tests and preserved additive ABI guarantees.
- **Completed**: Extract deterministic `cResourceCount` resource update-time accumulation policy (`update_time`/wrapper entrypoint decisions) behind additive Rust FFI
- **Impacted files/modules**: `avida-core/source/main/cResourceCount.h`, `avida-core/source/main/cResourceCount.cc`, `avida-core/include/private/rust/running_stats_ffi.h`, `rust/avida-rust/src/resource_count_helpers.rs`, `avida-core/source/targets/unit-tests/main.cc`
- **Result**: Routed update-time delta passthrough and wrapper global-only mode policy through new Rust helpers (`avd_rc_update_time_delta`, `avd_rc_wrapper_global_only_flag`) while preserving C++ orchestration, resource update sequencing, and state ownership; added Rust/C++ boundary tests for finite/NaN/Inf passthrough and wrapper mode mapping behavior.
- **Next candidate**: Extract deterministic `cResourceCount` gradient variable inflow setter call policy (`SetGradPlatVarInflow`) behind additive Rust FFI while preserving C++ ownership, context threading, and direct setter semantics.

- **Title**: Keep new FFI modules aligned with shared pointer-accessor macro pattern
- **Impacted files/modules**: `rust/avida-rust/src/common.rs`, future `rust/avida-rust/src/*` FFI modules
- **Risk/impact**: The shared macro now removes duplicate pointer boilerplate for current handles, but new modules can regress if they reintroduce ad hoc null-check/deref wrappers.
- **Suggested follow-up**: Require new FFI handle wrappers to use `common.rs` macro-generated accessors and add a small reviewer checklist item for this rule.
- **Priority**: low
