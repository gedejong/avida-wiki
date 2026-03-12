# Rust Migration Waves - Completed Archive

Archived on 2026-03-11 from `rust-migration-waves.md`.

## Completed Wave Entries

- `cRunningStats` (Rust-only; legacy fallback removed)
- `cRunningAverage` (Rust-only; legacy fallback removed)
- `cDoubleSum` (Rust-only; legacy fallback removed)
- `cWeightedIndex` (Rust-only; legacy fallback removed)
- `cOrderedWeightedIndex` (Rust-only; legacy fallback removed)
- `cHistogram` (Rust-only; legacy fallback removed)
- `cBitArray` (completed strict slice, Rust-only; legacy fallback removed)

- `cResourceCount` scheduling boundary policy lock (completed): explicit truncation/saturation/invalid-step semantics in Rust helper math with boundary parity fixtures (negative, exact-boundary, NaN/Inf, large-ratio saturation) in Rust+C++ unit tests
- `cResourceCount` spatial scheduling helper derivation (completed): Rust-backed deterministic spatial update-count delta derivation for `DoUpdates` (`m_spatial_update - m_last_updated`) with explicit saturation parity fixtures while keeping `DoSpatialUpdates`/`FlowAll`/`StateAll` in C++
- `cResourceCount` setup precalc table derivation (completed): Rust-backed additive helper for deterministic `Setup` recurrence table fill (`decay_precalc`/`inflow_precalc`) while preserving C++ table ownership and update-loop usage
- `cResourceCount` non-spatial step-application kernel (completed): Rust-backed deterministic helper for chunked + remainder recurrence application used by `DoNonSpatialUpdates`, preserving C++ ownership while centralizing update math parity
- `cSpatialResCount` source/sink scalar helper extraction (completed): Rust-backed additive helpers now compute source per-cell distribution and clamped sink/cell-outflow deltas used by `Source`/`Sink`/`CellOutflow`, while C++ retains grid traversal, index resolution, and state mutation ownership
- `cSpatialResCount` wrapped index helper extraction (completed): Rust-backed additive helper now computes wrapped rectangle coordinate-to-element index mapping used by `Source`/`Sink`, while C++ retains loop traversal, mutation calls, and state ownership
- `cSpatialResCount` cell-list valid-id helper extraction (completed): Rust-backed additive helpers now centralize strict (`< size`) and legacy SetCellList (`<= size`) cell-id bounds policies used by `CellInflow`/`CellOutflow`/`SetCellList`, while C++ retains list traversal and state mutation ownership.
- `cResourceHistory` deterministic entry helpers (completed): Rust-backed exact/non-exact update selection and bounds-safe value lookup used by `getEntryForUpdate`, `GetResourceCountForUpdate`, and `GetResourceLevelsForUpdate` while keeping file loading and state ownership in C++
- `cEventList` deterministic parse helpers (completed): Rust-backed trigger classification and timing tuple parsing (`begin`/`all`/`once`/`end` and numeric forms) routed through additive C ABI while preserving C++ event creation/state mutation ownership
- `Data::TimeSeriesRecorder` typed parse-policy parity hardening (completed): Rust typed getter coercion now matches legacy `Apto::StrAs` semantics (`bool` exact true aliases only; `int`/`double` C-style coercion including partial/hex/exponent forms) with shared Rust+C++ matrix fixtures and unchanged ABI surface
- `Data::Package` primitive formatting parity hardening (completed): Rust-backed `Wrap<bool/int/double>::StringValue` paths now have expanded boundary/threshold parity matrices (signed zero, denormals, exponent cutovers, integer limits, NaN/Inf) locked against legacy `Apto::AsStr` behavior
- `cBitArray` selective `bitvec` evaluation (completed, no-adopt): Added focused Criterion benchmark matrix for `shift`/`increment`/`count` workloads plus expanded Rust parity matrices across binary/unary ops and edge widths; benchmark deltas were mixed/marginal, so production internals remain on the current custom bit-field path with stronger decision evidence.
- Backtrace-enabled CI validation (completed): Added a dedicated CI smoke leg that explicitly sets `AVIDA_ENABLE_BACKTRACE=1` for configure/reconfigure + build coverage, and hardened vendored `backward-cpp` alias-target creation to avoid duplicate-target failures when backtrace mode is enabled.
- `cSpatialResCount` deterministic helper extraction (completed): Rust-backed additive helpers now normalize inflow/outflow spans and compute per-neighbor flow scalar math used by `CheckRanges` and `FlowMatter`, with C++ retaining traversal/state mutation ownership and parity guards in Rust+C++ unit tests.
- `Data::TimeSeriesRecorder` FFI guardrail cleanup (completed): Rust FFI entry points now consistently use shared handle access/output patterns from `common.rs` for typed/string getters, preserving ABI/coercion behavior while tightening null-handle/out-param stability checks.
- Provider/history deterministic readability hardening (completed): Refactored `provider_helpers` parsing internals to a structured parse result and clarified `resource_history_helpers` nearest-index iteration semantics, preserving C ABI/return policies with expanded duplicate-index parity coverage.
- Rust coverage scope expansion gate (completed): Coverage gating now writes a reusable summary artifact, enforces representative module presence checks to prevent accidental scope drift, and raises the CI line threshold from 80% to 82% with passing evidence.
- Build/configure robustness hardening (completed): default low-noise configure path with optional backtrace opt-in, CI reconfigure smoke check, and stable Linux static link-order preservation for `aptostatic` resolution
- Consistency fixture hardening (completed): cross-platform determinism stabilizations for `sex` and `shaded_green_beard_instructions` via fixture-local knob pinning, narrower intent-focused output assertions, and richer CI diagnostics artifacts
- `cSpatialResCount::SetPointers` neighbor-link helper extraction (completed): Rust-backed additive helper now derives each neighbor entry (`elempt`, `xdist`, `ydist`, `dist`) including GRID masking semantics, while C++ retains pointer ownership and traversal loops; parity is locked with Rust+C++ slot-matrix tests across corners/edges/interior and degenerate dimensions.
