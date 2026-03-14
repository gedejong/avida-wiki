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
- **Next candidate**: Extract deterministic `cResourceCount` resource update-time accumulation policy (`update_time`/`spatial_update_time` increment and wrapper entrypoint decisions) behind additive Rust FFI while preserving C++ sequencing and state ownership.

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

