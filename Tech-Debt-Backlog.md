# Tech Debt Backlog

This backlog captures follow-up improvements discovered during the C++ to Rust migration work.

## Entry template

- **Title**:
- **Impacted files/modules**:
- **Risk/impact**:
- **Suggested follow-up**:
- **Priority**: low | medium | high

## Current backlog

- Completed entries have been moved to `documentation/archive/tech-debt-backlog-completed.md`.

### Native port entries (2026-11-12)

- **Completed**: Standalone native Rust port of the simulator, GUI and TUI in the untracked workspace `rust/avida-native`
- **Impacted files/modules**: `rust/avida-native/` (17 crates; 189,119 src lines in 306 files, 12,990 test lines in 42 files, 853 `#[test]` attributes), `rust/avida-native/ARCHITECTURE.md`, `rust/avida-native/docs/ontology.md`
- **Result**: A `avida` binary that needs no C++ runs population runs and analyze mode, and reproduces the reference binary `cbuild/work/avida` byte for byte on the consistency suite over `avida-core/tests/*` (last measured 2026-11-12: 121 tests, 85 same, 34 trivial differences, 0 DIFF, 1 unsupported because the reference crashes on `SLICING_METHOD 4`, 1 reference timeout), on 76 pinned names in `crates/avida/tests/consistency_passing.txt`, on 242 action fixture cases under `crates/avida/tests/actions/`, and on instruction-trace comparisons in `crates/hardware/tests`. Default 3000-update run, seed 42: reference about 57 to 64 s user, native about 47 s, identical output; with gene transfer on (46,000 transfers), reference 41 to 42 s, native 34 to 36 s, identical output. Details in `rust-migration-waves.md`, section "Native port wave (2026-11-12)".
- **Next candidate**: resolve the open decisions below, starting with whether the native port supersedes the FFI migration, because that decision determines whether any further `cHardwareCPU.cc` handler work is worth doing.

- **Title**: Open decision (maintainer): does the native port supersede the FFI migration in `rust/avida-rust`?
- **Impacted files/modules**: `rust/avida-rust/`, `avida-core/source/cpu/cHardwareCPU.cc`, `avida-core/source/cpu/cHardwareFFI.cc`, `rust-migration-*.md`, `CLAUDE.md` (migration state and PO priorities)
- **Risk/impact**: The FFI track (807 `avd_*` exports, 372/518 CPU handlers delegating, roughly 28K lines of Rust) and the native track solve the same problem in two ways. If both continue, effort on the remaining 146 handlers and on `Divide_Main`/`Allocate_Main` ownership (FFI Wave 14) duplicates work the native `hardware` and `world` crates already contain. If the native port is accepted, that FFI work becomes moot; if it is not, the native workspace needs an owner.
- **Suggested follow-up**: Maintainer decides. Until then, treat the FFI "Next candidate" entries in `rust-migration-waves.md` (mating-type and `Inst_Repro` handlers, then `Inst_Collect*`) as paused rather than cancelled, and do not start new FFI slices. `CLAUDE.md` still lists those FFI priorities; it was not edited as part of this documentation update.
- **Priority**: high

- **Title**: Open decision (maintainer): commit strategy for the untracked workspace
- **Impacted files/modules**: `rust/avida-native/` (including `Cargo.lock`, `docs/ontology.json`, `docs/test_matrix.json`, `target/`), `.gitignore`
- **Risk/impact**: The whole workspace exists only in the working tree. A lost working directory loses all of it. A single commit of 189K lines is hard to review; `target/` is excluded by `.gitignore`.
- **Suggested follow-up**: Decide between one import commit per crate in layer order (`apto` to `world`, then `analyze`, `script`, `avida`, then `session`, `viewmodel`, `gui`, `tui`) and a single import commit with the architecture documents first. `target/` is already ignored (`.gitignore` line 124). Stage files explicitly, as the slice workflow requires, and avoid unrelated submodule pointer changes.
- **Priority**: high

- **Title**: CI for the native workspace
- **Impacted files/modules**: `.github/workflows/ci.yaml`, `rust/avida-native/`, `avida-core/tests/*`
- **Risk/impact**: The existing lanes (`tests`, `tests-rust`, `coverage-rust`, `perf-rust`, `ffi-analysis-rust`) cover `rust/avida-rust` and the C++ build only. Nothing automated guards the native workspace, so the byte-for-byte claims above rest on manual runs.
- **Suggested follow-up**: Add a lane running, in `rust/avida-native`, `cargo fmt --check`, `cargo clippy --workspace --all-targets -- -D warnings`, `cargo test --workspace`, and a second lane that builds the reference binary and runs the consistency suite (the 121-test comparison, with the timestamp and path normalisation the suite applies). Decide whether `gui` and `tui` build in CI (they need system libraries on Linux) or are excluded with `--exclude`. Record the suite result as a CI artefact so the "last measured" figures can be refreshed from CI instead of by hand. These gates have not yet been run by this update.
- **Priority**: high

- **Title**: Open decision (maintainer): retire `rust/avida-rust` and the C++ once the port is accepted
- **Impacted files/modules**: `rust/avida-rust/`, `avida-core/`, `libs/apto/`, `cbuild/`, `build_avida`, `run_tests`, `abi/avd_symbols_baseline.txt`
- **Risk/impact**: The C++ binary is the behavioural reference for every validation above, and it is built from the C++/FFI hybrid. Removing either before acceptance removes the oracle. The retirement policy in `rust-migration-execution-playbook.md` (stabilised through CI, no remaining call sites, isolated reviewable diff) was written for the FFI track and needs a native-port reading.
- **Suggested follow-up**: Define acceptance criteria first (for example zero DIFF on the suite for a stated number of weeks, the feature gaps below closed or accepted, human review of the front ends). Then archive rather than delete: keep a tag of the last hybrid commit, and keep a frozen reference binary for the consistency suite.
- **Priority**: medium

- **Title**: Remaining feature gaps and `unsupported:` stops in the native port
- **Impacted files/modules**: `rust/avida-native/crates/hardware/src`, `rust/avida-native/crates/world/src`, `rust/avida-native/crates/analyze/src` (about 140 stop sites in all)
- **Risk/impact**: The stops were classified on 2026-11-12 by reading each against the C++ at `cpp-final`. About 95 guard undefined behaviour or a crash in the C++ (out-of-bounds or uninitialised reads, use after free, NULL dereference, division by zero, endless loops) and stay, by the maintainer's ruling of the same day: where the C++ is undefined the port stops with a message that names the C++ site. The stops where the C++ is defined were closed on 2026-11-13, each with regression cases compared against the reference binary (`gap_*`, `phenplast_public_good`): three event-argument errors now print the C++ message and exit 1; a deadly or predatory gradient hitting the running organism under speculative execution or implicit reproduction; a damaging gradient during random-resource and group-resource reads; `SetRedundancy` and `SetProbFail` on every hardware type and with parasites; `InjectClone` and `MixPopulation` with avatars; self-communication into a bounded receive buffer; `ChangeEnvironment` with global deme resources; `consume-public-good` in a plasticity test beside the running organism. Analyze-mode set-up is complete: `avida -a` runs every population set-up through `world/src/analyze_setup.rs` (`an2_*` fixtures). Closed later on 2026-11-13: a damaging gradient reached through the gene-transfer metabolism path (`gap_hgt_damaging_kill`, `gap_hgt_damaging_source_parent`), and the `MAX_PREY_BT` kill of a population organism by `set-rand-p-ft-once` in a divide-time test CPU (`gap_exp_max_prey_bt_test_cpu`; the kill is carried out when the test ends, see `PORTING.md`). Still stopping although the C++ is defined: that kill from a plasticity test or an event-time test, of the dividing parent itself, or a second one in the same test run; the merit-to-zero cases of a damaging gradient; `kill_organism` without any organism state (no call path found). The stop for a test CPU whose call chain did not record the forage target is unreachable (every call site was read) and stays as a guard. `ChangeEnvironment` with spatial deme resources is undefined in the C++ and stays. Eleven stops that could not fire were removed (instruction fallbacks behind the instruction-set check, three missing-deme branches, four interface methods nothing calls); the remaining stops were not reclassified one by one.
- **Suggested follow-up**: With `VERBOSITY 3`, `ChangeEnvironment` prints resource set-up numbers in the stream's sticky format in the C++ (`50.00000`) and plainly here (`50`).
- **Priority**: medium

- **Title**: Head display for BCR and GP8 hardware in the GUI and TUI debuggers
- **Impacted files/modules**: `rust/avida-native/crates/gui/src/model/debugger.rs`, `rust/avida-native/crates/tui/src/debugger.rs`, `rust/avida-native/crates/tui/src/ui/debug.rs`, `rust/avida-native/crates/viewmodel`
- **Risk/impact**: The debuggers show the heads of the heads CPU. BCR and GP8 organisms run in both front ends but their head state is not displayed (stated as a known gap by the front-end work; the exact display behaviour was not re-verified in this update).
- **Suggested follow-up**: Extend the viewmodel with a hardware-neutral head list and render it in both debuggers, with a snapshot test per hardware type.
- **Priority**: low

- **Title**: Human review of the GUI and TUI in real windows and terminals
- **Impacted files/modules**: `rust/avida-native/crates/gui`, `rust/avida-native/crates/tui`, `rust/avida-native/crates/viewmodel`
- **Risk/impact**: Both front ends have unit and snapshot tests (`gui` 104, `tui` 105, `viewmodel` 28 `#[test]` attributes) but those cannot judge layout, legibility, input handling, resize behaviour or terminal compatibility. No human has reviewed them in a real window or terminal.
- **Suggested follow-up**: A maintainer session covering: egui at several window sizes and display scales, ratatui in at least two terminal emulators and over ssh, a long run for responsiveness of the session thread, and the pause and step controls against the `Stepper`. Record findings as separate entries.
- **Priority**: medium

- **Title**: Reconcile counts and snapshots across native-port documents
- **Impacted files/modules**: `rust/avida-native/ARCHITECTURE.md`, `rust/avida-native/docs/ontology.md`, `rust-migration-waves.md`
- **Risk/impact**: The documents disagree on small facts: action names (423 in `ARCHITECTURE.md`, 432 in `ontology.md`, 427 by a grep of `Register<...>("Name")` calls), suite size (`ARCHITECTURE.md` mentions 123 directories while the suite figure is 121 tests, and `ontology.md` lists 122 directories), and snapshot dates (`ontology.md` 2026-11-10, `ARCHITECTURE.md` 2026-11-11). `ontology.md` percentages and instruction-handler counts were generated from a smaller snapshot (143 files) and predate the `session`, `viewmodel`, `gui` and `tui` crates.
- **Suggested follow-up**: Regenerate `ontology.json` and `ontology.md` from the current tree, and have `ARCHITECTURE.md` cite one action count with the command that produces it.
- **Priority**: low

- **Title**: Parallel execution mode (`EXECUTION_MODE 1`): timings, empty cells, further speed
- **Impacted files/modules**: `rust/avida-native/crates/world/src/parallel.rs`, `rust/avida-native/crates/hardware/src/parallel.rs`, `rust/avida-native/crates/world/src/run.rs`, `rust/avida-native/crates/config/src/port_settings.rs`, `rust/avida-native/crates/avida/tests/parallel.rs`, `rust/avida-native/tools/parallel_stats.py`
- **Risk/impact**: The opt-in mode is the one approved departure from parity (`PORTING.md`); the exact mode stays the default and the oracle. Over 16 seeds per mode and 5000 updates it matches the exact mode on all nine outcomes compared (fitness, genome length, tasks held, first NOT and NAND, generation, gestation, organisms, instructions per update; all p of at least 0.27). An earlier excess of empty cells came from deterministic per-update budgets, which keep related organisms in phase; budgets are now drawn at random, as the lottery scheduler does (`ARCHITECTURE.md`). On a quiet machine the default 3000-update run takes 47.2 s exact, 28.6 s on one thread and 18.1 s on four or eight. Demes run in the mode since 2026-11-13: replication counts match the exact mode under all four implicit triggers, and a deme generation takes about one update (1.5% to 3%) longer (`ARCHITECTURE.md`, "Demes"). Hardware other than the heads CPU runs every instruction through the sequential phase and gains nothing.
- **Suggested follow-up**: The speed items listed in `ARCHITECTURE.md` (a parallel `update_organism_stats`, output off the main thread, fewer rounds per update, the main thread in the pool), since threads beyond four add nothing; set `POOL_MIN_ORGANISMS` from measurements on an idle machine; deme support. A small difference in births per update from a common starting population (173.4 against 176.3) is described in `ARCHITECTURE.md` and not removed.
- **Priority**: medium

- **Title**: `Task_Optimize` edge cases and the multiply-add audit of `analyze` and `script` (native port, 2026-11-12)
- **Impacted files/modules**: `rust/avida-native/crates/tasks/src/eval_misc.rs`, `crates/tasks/src/task_lib.rs`, `crates/tasks/src/context.rs` (`TaskWorld::task_stdout`), `crates/hardware/src/task_adapter.rs` and `crates/world/src/host.rs` (follow-up only)
- **Risk/impact**: `optimize` now evaluates as the reference binary's helper `avd_task_eval_optimize` does (unknown function, non-positive proportional terms, a zero binary total, reads past the stored outputs; checked in analyze mode on 12 reactions by 5 genomes and 16 further argument sets by 4 genomes: `task_quality`, merit and standard output identical), and arguments on which the reference crashes, aborts or hangs stop at load with `unsupported:` (11 cases, each run on the reference). The `analyze` and `script` multiply-add sites all match the reference disassembly; nothing changed there. `OrgTaskWorld` and `WorldHost` forward `TaskWorld::task_stdout`, so the `quality > 1!` lines of a population run are queued with the other output; the test CPU's `TestHost` does not forward it. The maintainer confirmed on 2026-11-12 that the reference binary is the behaviour to keep.
- **Suggested follow-up**: compare a population run with an `optimize` reaction whose `minFx` exceeds `Fx` against the reference, standard output included.
- **Priority**: low

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

- **Title**: Expand phenotype grouping consistency fixture with additional sequence/task diversity
- **Impacted files/modules**: `avida-core/tests/analyze_printphenotypes_grouping/config/analyze.cfg`, `avida-core/tests/analyze_printphenotypes_grouping/expected/data/total_task_count`
- **Risk/impact**: Current fixture validates grouping/count semantics on a compact representative case, but does not yet cover broader reaction/task combinations.
- **Suggested follow-up**: Add 2-3 additional sequence families (including edge-case non-viable and high-task variants) while keeping expected output compact and deterministic.
- **Priority**: low

