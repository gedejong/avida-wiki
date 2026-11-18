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

### Configuration format (2026-11-18)

- **Title**: Move the configuration files to YAML with the settings grouped (maintainer's request, 2026-11-18: "I'd be ok with porting everything over, instead of maintaining backward compatibility")
- **Impacted files/modules**: `rust/avida-native/crates/config` (614 settings in the flat `avida.cfg`), `crates/environment` (the `RESOURCE` and `REACTION` lines of `environment.cfg`), `crates/script` (`events.cfg`, `analyze.cfg`), `crates/hardware` (instruction set files); the 34 files of `avida-core/support/config`, the 391 `.cfg` files under `avida-core/tests`, the app's bundled configuration (`crates/gui/src/model/new_experiment.rs`), the settings editors of both front ends, `-set NAME VALUE` on the command line, `docs/`, the lab folders.
- **Risk/impact**: The consistency suite compares output, so a change of input format leaves the reference snapshot valid as long as every test's settings convert exactly; a converter that is run over all 391 files and checked by the suite staying at no DIFF is the proof. Decided by the maintainer on 2026-11-18: one YAML file per experiment (settings, environment, events and instruction set together); organism files (`.org`) and analyze scripts stay as they are; `-set` names a setting by its dotted path (such as `mutation.copy_prob`); old files are refused with a pointer to the converter.
- **Suggested follow-up**: (1) a grouping proposal for the 614 settings, taken from the existing section comments of `avida.cfg`, for the maintainer to edit; (2) a typed schema in `crates/config` with unknown keys refused; (3) a converter `avida convert DIR`; (4) convert support, tests and labs, suite at no DIFF; (5) remove the old readers.
- **Priority**: medium

### Composed-computation entries (2026-11-15)

- **Completed**: Stage 3 experiment, modular heads set against stock set on the adder ladder (extensions beyond the C++; `docs/guides/experiment-composed-computation.md`)
- **Impacted files/modules**: `avida-core/support/config/instset-heads-modular.cfg`, `avida-core/support/config/environment-adder-ladder.cfg`, `rust/avida-native/crates/tasks/src/eval_field.rs`, `rust/avida-native/crates/world/src/knockout.rs`, `rust/avida-native/crates/viewmodel/src/compare/`
- **Result**: 20 runs (10 seeds per arm, 100,000 updates, 60 x 60, exact mode) finished. Negative: the highest rung reached was HALFADD_PLANES, by 1 modular seed and 3 stock seeds (Fisher exact p = 0.58); no rung above it by either arm. In 12 runs NOT or NAND appeared and was lost before update 4,000, and 3 more never held a rung, with genomes shrunk from 100 to 19 to 24 instructions.
- **Next candidate**: stage 4 (sex, population size, changing environments, one factor at a time), prepared in the lab folder `14-search-conditions` and waiting for the maintainer's decision on scope and run time.

- **Completed**: `PrintReuseSurvey`, reuse measures over the most abundant task-performing genotypes and a count of subroutine sites
- **Impacted files/modules**: `rust/avida-native/crates/world/src/knockout_run.rs`, `rust/avida-native/crates/world/src/knockout.rs`, `rust/avida-native/crates/hardware/src/call_probe.rs`
- **Result**: Answers the two weaknesses stage 3 showed (the dominant genotype often performs no task; shared sites count a straight pipeline as reuse). On the saved population of the one modular run that climbed (seed 3) the five surveyed genotypes have 0 subroutine sites; the hand-written `modular-subroutine.org` has 2.
- **Next candidate**: read the survey in the arm-comparison view (it reads `reuse.dat` only; the maintainer left the comparison view as it is on 2026-11-15).
- **Checked 2026-11-15**: the genotype of modular seed 3 with two `call` sites and 0 subroutine sites (`lvtnpnC...`, 61 instructions) is measured correctly. Neither `call` is followed by a nop, so each jumps forward by the number in BX, here 0, and execution simply continues; the genome has no `return`. Every site therefore runs under at most one innermost call site. The "1 distinct target" of that row is the single category that label-less calls share, not a shared subroutine.

- **Title**: Maintainer's decisions on the backlog (2026-11-15)
- **Impacted files/modules**: this file
- **Risk/impact**: Decisions taken while reviewing the list, so that later sessions do not ask again. Reproducibility across platforms: wait, keep filed. Parallel mode: improve it. `unsupported:` stops: find what the C++ intended at each stop and port towards that intent, where one can be found. Open-endedness measures: undecided. Comparison view defects and the debugger head display for BCR and GP8: leave. Housekeeping entries (counts across documents, `Task_Optimize` edge cases, the phenotype grouping fixture): fix.
- **Suggested follow-up**: Work in that order of the maintainer's interest: parallel mode, then the intent of the `unsupported:` stops, then the housekeeping. Each intent-based change of a stop departs from the C++ and needs a `PORTING.md` entry that states the C++ behaviour, the inferred intent and the evidence for it.
- **Priority**: medium

- **Title**: Ancestry measures are not defined for two-parent reproduction
- **Impacted files/modules**: `rust/avida-native/crates/world/src` (MODES, phylogeny metrics), `rust/avida-native/crates/systematics/src/genotype_arbiter.rs`
- **Risk/impact**: `PrintMODESData` and `PrintPhylogenyMetrics` assume a tree. With `divide-sex` the ancestry is a pedigree, the historic list grows without bound (86,936 historic genotypes for 3,522 living after 3,200 updates of a 60x60 world), and `DROP_EXTINCT_GENOTYPES 1` avoids the memory by switching the measures off. The `sex` arm of the stage 4 experiment therefore reports no complexity or phylogeny measure.
- **Suggested follow-up**: Define the measures on pedigrees (for example by following one parent, or per-site ancestry), or track a bounded number of generations.
- **Priority**: low

- **Title**: `DROP_EXTINCT_GENOTYPES`: untested corners
- **Impacted files/modules**: `rust/avida-native/crates/avida/tests/drop_extinct.rs`, `rust/avida-native/crates/viewmodel/src/capture.rs`, `rust/avida-native/crates/world/src/pop_interface.rs`
- **Risk/impact**: The stop of the donate instructions' kin-distance check has no test. Neither front end has been run with the setting on; the Ancestry layer is left out of the frame and the code that handles a missing layer was read, not exercised. Parallel-mode identity is tested on a 20x20 world only.
- **Suggested follow-up**: A test with `MAX_DONATE_KIN_DIST` set, and a rendered sweep of both front ends with the setting on.
- **Priority**: low

- **Title**: CI covers one platform and skips the terminal scenarios
- **Impacted files/modules**: `.github/workflows/ci.yaml`, `rust/avida-native/crates/tui/tests/pty.rs`, `rust/avida-native/tools/suite-ref.tar.xz`
- **Risk/impact**: Format, lint, tests and the consistency suite run on macOS arm64 only; Linux is covered by the desktop app's lane alone. The pseudo-terminal scenarios are skipped on CI (`AVIDA_SKIP_PTY_TESTS`) because they depend on timing. The suite compares against a snapshot that must be repacked by hand when a test under `avida-core/tests` changes. The workflow's actions run on a Node version GitHub has deprecated.
- **Suggested follow-up**: Make the terminal scenarios wait on output instead of on time so that CI can run them; update the actions' versions; have the suite fail with a clear message when a test has no entry in the snapshot (it reports NO-REF today, which fails the run).
- **Priority**: low

- **Title**: Small defects in the arm-comparison view
- **Impacted files/modules**: `rust/avida-native/crates/viewmodel/src/compare/`, `rust/avida-native/crates/gui/src/`
- **Risk/impact**: A median bar can cover a dot of the same value; the "highest rung" measure is printed as the rung's number plus one with 0 for none, which reads as an off-by-one next to the rung table.
- **Suggested follow-up**: Offset the median bar and print rung names for the rung measures.
- **Priority**: low

### Native port entries (2026-11-12)

- **Completed**: Standalone native Rust port of the simulator, GUI and TUI in the untracked workspace `rust/avida-native`
- **Impacted files/modules**: `rust/avida-native/` (17 crates; measured 2026-11-13: 199,702 src lines in 315 files, 15,885 test lines in 50 files, 945 `#[test]` attributes), `rust/avida-native/ARCHITECTURE.md`, `rust/avida-native/docs/ontology.md`
- **Result**: A `avida` binary that needs no C++ runs population runs and analyze mode, and reproduces the reference binary `cbuild/work/avida` byte for byte on the consistency suite over `avida-core/tests/*` (last measured 2026-11-12: 121 tests, 85 same, 34 trivial differences, 0 DIFF, 1 unsupported because the reference crashes on `SLICING_METHOD 4`, 1 reference timeout), on 76 pinned names in `crates/avida/tests/consistency_passing.txt`, on 242 action fixture cases under `crates/avida/tests/actions/`, and on instruction-trace comparisons in `crates/hardware/tests`. Default 3000-update run, seed 42: reference about 57 to 64 s user, native about 47 s, identical output; with gene transfer on (46,000 transfers), reference 41 to 42 s, native 34 to 36 s, identical output. Details in `rust-migration-waves.md`, section "Native port wave (2026-11-12)".
- **Next candidate**: resolve the open decisions below, starting with whether the native port supersedes the FFI migration, because that decision determines whether any further `cHardwareCPU.cc` handler work is worth doing.

- **Title**: Output on Linux x86-64 is not byte-identical to macOS arm64
- **Impacted files/modules**: `rust/avida-native/crates/apto/src/rng.rs`, `rust/avida-native/crates/apto/src/stat.rs`, `.github/workflows/ci.yaml`
- **Risk/impact**: Since 2026-11-15 CI runs format, lint, tests and the consistency suite (from a snapshot of the reference output, `tools/suite_ref.py`) on macOS arm64, and passes there. On Linux x86-64 two `apto` tests fail: one normal deviate of 200 differs in the last bit (the platform's `log`/`sqrt`), and a NaN has the other sign. A run on Linux can therefore diverge from the same seed on macOS, and nothing tests Linux apart from the desktop app's lane.
- **Decision 2026-11-15**: the maintainer lets this wait until a later stage; keep it filed.
- **Suggested follow-up**: Decide whether results must be reproducible across platforms. If so, replace the platform math calls in `apto` by a portable implementation (for example the `libm` crate) and measure what that does to the suite; if not, say so in the user documentation.
- **Priority**: medium

- **Title**: Remaining feature gaps and `unsupported:` stops in the native port
- **Impacted files/modules**: `rust/avida-native/crates/hardware/src`, `rust/avida-native/crates/world/src`, `rust/avida-native/crates/analyze/src` (about 140 stop sites in all)
- **Risk/impact**: The stops were classified on 2026-11-12 by reading each against the C++ at `cpp-final`. About 95 guard undefined behaviour or a crash in the C++ (out-of-bounds or uninitialised reads, use after free, NULL dereference, division by zero, endless loops) and stay, by the maintainer's ruling of the same day: where the C++ is undefined the port stops with a message that names the C++ site. The stops where the C++ is defined were closed on 2026-11-13, each with regression cases compared against the reference binary (`gap_*`, `phenplast_public_good`): three event-argument errors now print the C++ message and exit 1; a deadly or predatory gradient hitting the running organism under speculative execution or implicit reproduction; a damaging gradient during random-resource and group-resource reads; `SetRedundancy` and `SetProbFail` on every hardware type and with parasites; `InjectClone` and `MixPopulation` with avatars; self-communication into a bounded receive buffer; `ChangeEnvironment` with global deme resources; `consume-public-good` in a plasticity test beside the running organism. Analyze-mode set-up is complete: `avida -a` runs every population set-up through `world/src/analyze_setup.rs` (`an2_*` fixtures). Closed later on 2026-11-13: a damaging gradient reached through the gene-transfer metabolism path (`gap_hgt_damaging_kill`, `gap_hgt_damaging_source_parent`), and the `MAX_PREY_BT` kill of a population organism by `set-rand-p-ft-once` in a divide-time test CPU (`gap_exp_max_prey_bt_test_cpu`; the kill is carried out when the test ends, see `PORTING.md`). Still stopping although the C++ is defined: a `MAX_PREY_BT` kill from a loop of test CPU runs (`PrintTaskSnapshot`, `PrintSoloTaskSnapshot`, `PrintDominantForagerGenotypes`, the `hist_*` test-world analyses) or from the precalculated phenotype, which need the loop to re-read the population after each test; `kill_organism` without any organism state (no call path found). Closed on 2026-11-15: the kill from an event-time test, from a plasticity test, and a second kill in one test. The `MAX_PREY_BT` pick of the dividing parent and the merit driven to zero by a damaging gradient are not defined in the C++ (both segfault in the reference; `gap_c4_damage_random_res` with `damage=1.0`) and stay. The stop for a test CPU whose call chain did not record the forage target is unreachable (every call site was read) and stays as a guard. `ChangeEnvironment` with spatial deme resources is undefined in the C++ and stays. Eleven stops that could not fire were removed (instruction fallbacks behind the instruction-set check, three missing-deme branches, four interface methods nothing calls); the remaining stops were not reclassified one by one.
- **Suggested follow-up**: With `VERBOSITY 3`, `ChangeEnvironment` prints resource set-up numbers in the stream's sticky format in the C++ (`50.00000`) and plainly here (`50`).
- **Priority**: medium

- **Title**: Head display for BCR and GP8 hardware in the GUI and TUI debuggers
- **Impacted files/modules**: `rust/avida-native/crates/gui/src/model/debugger.rs`, `rust/avida-native/crates/tui/src/debugger.rs`, `rust/avida-native/crates/tui/src/ui/debug.rs`, `rust/avida-native/crates/viewmodel`
- **Risk/impact**: The debuggers show the heads of the heads CPU. BCR and GP8 organisms run in both front ends but their head state is not displayed (stated as a known gap by the front-end work; the exact display behaviour was not re-verified in this update).
- **Suggested follow-up**: Extend the viewmodel with a hardware-neutral head list and render it in both debuggers, with a snapshot test per hardware type.
- **Priority**: low

- **Title**: Reconcile counts and snapshots across native-port documents
- **Impacted files/modules**: `rust/avida-native/ARCHITECTURE.md`, `rust/avida-native/docs/ontology.md`, `rust-migration-waves.md`
- **Risk/impact**: The documents disagree on small facts: action names (423 in `ARCHITECTURE.md`, 432 in `ontology.md`, 427 by a grep of `Register<...>("Name")` calls), suite size (`ARCHITECTURE.md` mentions 123 directories while the suite figure is 121 tests, and `ontology.md` lists 122 directories), and snapshot dates (`ontology.md` 2026-11-10, `ARCHITECTURE.md` 2026-11-11). `ontology.md` percentages and instruction-handler counts were generated from a smaller snapshot (143 files) and predate the `session`, `viewmodel`, `gui` and `tui` crates.
- **Suggested follow-up**: Regenerate `ontology.json` and `ontology.md` from the current tree, and have `ARCHITECTURE.md` cite one action count with the command that produces it.
- **Priority**: low

- **Title**: Parallel execution mode (`EXECUTION_MODE 1`): timings, empty cells, further speed
- **Impacted files/modules**: `rust/avida-native/crates/world/src/parallel.rs`, `rust/avida-native/crates/hardware/src/parallel.rs`, `rust/avida-native/crates/world/src/run.rs`, `rust/avida-native/crates/config/src/port_settings.rs`, `rust/avida-native/crates/avida/tests/parallel.rs`, `rust/avida-native/tools/parallel_stats.py`
- **Risk/impact**: The opt-in mode is the one approved departure from parity (`PORTING.md`); the exact mode stays the default and the oracle. Over 16 seeds per mode and 5000 updates it matches the exact mode on all nine outcomes compared (fitness, genome length, tasks held, first NOT and NAND, generation, gestation, organisms, instructions per update; all p of at least 0.27). An earlier excess of empty cells came from deterministic per-update budgets, which keep related organisms in phase; budgets are now drawn at random, as the lottery scheduler does (`ARCHITECTURE.md`). On a quiet machine the default 3000-update run takes 47.2 s exact, 28.6 s on one thread and 18.1 s on four or eight. Demes run in the mode since 2026-11-13: replication counts match the exact mode under all four implicit triggers, and a deme generation takes about one update (1.5% to 3%) longer (`ARCHITECTURE.md`, "Demes"). Hardware other than the heads CPU runs every instruction through the sequential phase and gains nothing.
- **Suggested follow-up**: The speed items listed in `ARCHITECTURE.md` (a parallel `update_organism_stats`, output off the main thread, fewer rounds per update, the main thread in the pool), since eight threads gain only about 10% over four (output tests and divides moved into the bursts on 2026-11-13 for environments without resources or with only plain per-cell grids; stock run 13–15 s on four threads); local output tests for predatory, deadly or damaging gradients, the one resource kind whose tests still run in the sequential phase (global pools, `PARTIAL` resources, other gradients and reactions on both a grid and a pool run in the bursts since 2026-11-14, `PORTING.md`); optionally settle pool tests in organism-clock order so that a consumed global pool read at an update boundary is not biased low (traced 2026-11-14 to round-end settlement, about x/(e^x - 1) for a fraction x of the pool taken per update, `PORTING.md`); set `POOL_MIN_ORGANISMS` from measurements on an idle machine; deme support. A small difference in births per update from a common starting population (173.4 against 176.3) is described in `ARCHITECTURE.md` and not removed.
- **Priority**: medium
- **Measured 2026-11-13 (parasites)**: the transsmt hardware ran every instruction in the sequential phase (100% sequential on a 160x160 host-parasite world). Burst support for it and parallel flow of plain grid resources were added the same day (`ARCHITECTURE.md`, "TransSMT in bursts" and "Scaling work of 2026-11-13"). Divides and the world-reaching IO of each gestation are still sequential and are now the limit beyond four threads.

- **Title**: `Task_Optimize` edge cases and the multiply-add audit of `analyze` and `script` (native port, 2026-11-12)
- **Impacted files/modules**: `rust/avida-native/crates/tasks/src/eval_misc.rs`, `crates/tasks/src/task_lib.rs`, `crates/tasks/src/context.rs` (`TaskWorld::task_stdout`), `crates/hardware/src/task_adapter.rs` and `crates/world/src/host.rs` (follow-up only)
- **Risk/impact**: `optimize` now evaluates as the reference binary's helper `avd_task_eval_optimize` does (unknown function, non-positive proportional terms, a zero binary total, reads past the stored outputs; checked in analyze mode on 12 reactions by 5 genomes and 16 further argument sets by 4 genomes: `task_quality`, merit and standard output identical), and arguments on which the reference crashes, aborts or hangs stop at load with `unsupported:` (11 cases, each run on the reference). The `analyze` and `script` multiply-add sites all match the reference disassembly; nothing changed there. `OrgTaskWorld` and `WorldHost` forward `TaskWorld::task_stdout`, so the `quality > 1!` lines of a population run are queued with the other output; the test CPU's `TestHost` does not forward it. The maintainer confirmed on 2026-11-12 that the reference binary is the behaviour to keep.
- **Suggested follow-up**: compare a population run with an `optimize` reaction whose `minFx` exceeds `Fx` against the reference, standard output included.
- **Priority**: low

- **Title**: Open-endedness measures and chart filtering (native port, 2026-11-14): what is left open
- **Impacted files/modules**: `rust/avida-native/crates/world/src/modes.rs`, `crates/world/src/phylo_actions.rs`, `crates/systematics/src/phylometrics.rs`, `crates/viewmodel/src/open_ended.rs`, `crates/viewmodel/src/filter.rs`, `crates/gui/src/model/filtering.rs`, `crates/gui/src/ui/{filter_bar,histogram,scatter,charts}.rs`
- **Risk/impact**: `PrintMODESData`, `PrintPhylogenyMetrics` and `SavePhylogeny` are extensions the C++ does not have (`PORTING.md`); they observe a run without changing it (tests pin byte-identical output with and without them, in both execution modes). Measured 2026-11-14: workspace 208,865 src lines, 1,034 `#[test]` attributes, 1,018 tests passed, suite 85 same, 34 trivial, no DIFF. Open: (1) the MODES validation reproduced the paper's qualitative behaviour for change, novelty and ecology but not its higher change in the Logic-9 environment, cause unknown; (2) the default persistence filter follows the reference code (`nearest`), the paper's wording is the option `any`, and the maintainer has not ruled which is the default; (3) the phylogeny measures were checked against brute force, not against `phylotrackpy`; (4) phenotypic volatility is not implemented, because the tracker keeps no task phenotype of extinct ancestors; (5) lineage depth is not a filter property, since no map layer carries it; (6) the terminal interface has no filtering; (7) hover tooltips, Shift-click and drags of the filter were tested through the test harness and rendered images, not in a real window; (8) an active filter adds about 2.8 ms of capture per frame on the simulation thread at 160x160.
- **Suggested follow-up**: maintainer's ruling on (2); a cross-check run for (3) on a machine with `phylotrackpy`; a depth layer in the recording format if (5) is wanted; move the filter's extra capture off the simulation thread if (8) shows in interactive runs.
- **Priority**: medium

- **Title**: Expand phenotype grouping consistency fixture with additional sequence/task diversity
- **Impacted files/modules**: `avida-core/tests/analyze_printphenotypes_grouping/config/analyze.cfg`, `avida-core/tests/analyze_printphenotypes_grouping/expected/data/total_task_count`
- **Risk/impact**: Current fixture validates grouping/count semantics on a compact representative case, but does not yet cover broader reaction/task combinations.
- **Suggested follow-up**: Add 2-3 additional sequence families (including edge-case non-viable and high-task variants) while keeping expected output compact and deterministic.
- **Priority**: low
