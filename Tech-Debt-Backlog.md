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

- **Title**: Extract deterministic `cSpatialResCount::FlowAll` per-neighbor transfer accumulation helper math behind additive Rust FFI
- **Impacted files/modules**: `avida-core/source/main/cSpatialResCount.cc`, `avida-core/include/private/rust/running_stats_ffi.h`, `rust/avida-rust/src/spatial_res_count_helpers.rs`, `avida-core/source/targets/unit-tests/main.cc`
- **Risk/impact**: Flow transfer math and guard semantics are sensitive to small behavior drift that can alter resource dynamics.
- **Suggested follow-up**: Add Rust helper(s) for deterministic per-neighbor transfer scalar/accumulation boundaries while preserving C++ grid traversal and mutation ownership; lock with Rust+C++ parity matrices for boundary and degenerate geometry cases.
- **Priority**: high

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

