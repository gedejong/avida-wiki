We welcome all those interested to contribute to the development and improvement of the Avida project. This developer’s guide will describe how to get started, the guidelines for contributions, and where to find ideas for what is needed.

## Project Structure
Avida consists of several modules/sub-projects.

* [Avida-Core Library](Development-|-avida-core)
* [Avida-Viewer Library](Development-|-avida-viewer)
* [Avida2 Frontend](Development-|-avida2)
* [The OS X/iOS Viewers](Development-|-viewer-macos)
* [The Windows Viewer](Development-|-viewer-windows)

### [Getting Started](Development-|-Getting-Started)
### [Coding Style Guide](Development-|-Coding-Style-Guide)


## Mini Tutorials
* [A Conceptual Introduction to C++](Development-|-Tutorial-|-C-Plus-Plus-Intro)
* [Guide to the Death/Birth Cycle](Development-|-Tutorial-|-Birth-Death-Cycle)
* [Guide to an Avida Organism Life Cycle](Development-|-Tutorial-|-Life-Cycle)
* [The Building Blocks of the Virtual CPU](Development-|-Tutorial-|-Genome)
* [The Environment Source Code](Development-|-Tutorial-|-Environment)
* [Instruction Implemention Checklist](Development-|-Tutorial-|-Instruction-Checklist)
* [Environment Task Implementation Checklist](Development-|-Tutorial-|-Task-Checklist)

## Rust Migration Standards (Strict)

All C/C++ to Rust migration slices must preserve a reversible dual-language path and must ship with tests and docs in the same change.

### Required FFI Invariants

- Use opaque handle APIs (`new/free`) at language boundaries.
- Treat null handles as safe no-op for mutators and `0`/empty for accessors.
- Never free memory across language boundaries. The creating side frees.
- Do not expose C++ templates, exceptions, or operator overloads across FFI.
- Keep legacy C++ implementation available behind a compile-time fallback toggle for at least one release window.

### Required Validation Gates Per Slice

- Default path: `./build_avida -DAVD_UNIT_TESTS:BOOL=ON`
- Default strict consistency tests: `./run_tests --mode=slave`
- Default unit tests: `./cbuild/work/unit-tests`
- Rust path: `AVD_ENABLE_RUST=1 ./build_avida -DAVD_UNIT_TESTS:BOOL=ON`
- Rust strict consistency tests: `./run_tests --mode=slave`
- Rust unit tests: `./cbuild/work/unit-tests`
- Rust crate gates (in `rust/avida-rust`): `cargo test`, `cargo fmt --check`, `cargo clippy -- -D warnings`

### External Crates First (Required Assessment)

For each new Rust migration slice, evaluate external crates before writing custom implementations.

- Prefer mature crates when they are measurably better on correctness, performance, or maintenance.
- Keep custom code only when crate behavior cannot preserve existing C++ parity/ABI expectations.
- Record crate candidates and accept/reject rationale in PR notes and `documentation/Tech-Debt-Backlog.md`.
- Any accepted crate must pass full parity gates (`build + unit-tests + run_tests + cargo test/fmt/clippy`) and must not change C ABI signatures.

Recommended candidates for upcoming/ongoing slices:

- Bit operations: `bitvec`
- Parsing/tokenization: `nom` or `winnow`
- Numeric/statistical helpers: `statrs` (when parity can be guaranteed)
- Fast string/number formatting (if needed after profiling): `itoa`, `ryu`
