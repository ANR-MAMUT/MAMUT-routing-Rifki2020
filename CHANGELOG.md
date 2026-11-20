# Changelog: Rifki2020 benchmark family

All notable changes to the Rifki2020 family are recorded here. The first entry records the creation of this repository. The sections after it hold the changelogs of the 2 former repositories. They keep every entry, fact, date, count and commit reference, with the wording edited for style and the headings moved 2 levels down.

## 2026-11: family repository

- The TDVRPTW and TDVRP variants of Rifki2020, kept until now in the 2 repositories `MAMUT-routing-TDVRPTW-Rifki2020` and `MAMUT-routing-TDVRP-Rifki2020`, form 1 repository, `MAMUT-routing-Rifki2020`, mounted in MAMUT-routing at `benchmarks/Rifki2020`. The histories of both former repositories are imported, each under the directory of its problem type, and joined by a merge commit. `history/` holds the maps from the former to the new commit identifiers.
- Instances and best-known solutions keep their former path after the problem type, for example `TDVRP/n=10/Rifki-10.vrp.json`.
- The ATF sidecars move to `sidecars/`, stored once for the 2 variants: 180 sidecars kept, 180 duplicate copies removed. In 360 instances the sidecar path field now holds a path relative to the repository root, for example `sidecars/n=10/Rifki-10.atf.json.gz`. No sidecar content changed, and the sidecar SHA-256 recorded in each instance is unchanged.
- The SHA-256 of the 360 instance files changed because of the rewritten path field. No instance identifier changed, and no best-known solution changed.
- The verification records of the 2 accepted community contributions move from `TDVRPTW/contributions/` to `contributions/`, unchanged. Their `instance` field still names the instance path relative to the root of the former TDVRPTW repository, and their `instance_sha256` refers to the instance bytes before the path field was rewritten.
- `mamut-collection.json` at the root marks the repository as a family collection (`"layout_version": 2`).
- This layout is read by `mamut-routing-lib` 0.13 and later, which is in development and not released yet. `mamut-routing-lib` 0.12 and earlier read the former layout, which stays available read-only in the 2 archived former repositories.
- The README, LICENSE, CHANGELOG and CITATION.cff files of the 2 variants are replaced by 1 file each at the root, and a NOTICE file is added.

## Former TDVRPTW repository

### Changelog of the Rifki2020 TDVRPTW BKS

All notable changes to the curated `Rifki2020` TDVRPTW best-known solutions (BKS) are recorded here. The objective is **Duration** (duration minimization), and the depot departure time of each route is a decision variable. Costs are the authoritative output of the canonical checker (`mamut_routing_lib.td.check_td_solution`): exact IEEE-754 double arithmetic, no epsilon thresholds, routes in canonical order (sorted by first customer), total summed in that order. Any strict improvement is therefore real. Reminder: time windows and vehicle attributes are the ones curated by Onyr, not the original [RCS20] TW files (see README.md), so these BKS are not comparable with results on the original setting.

#### 2026-11-13

Accepted [the Rifki-11 contribution of Farah Abdallah, Leen Qawas and Bana Mohammad](https://github.com/ANR-MAMUT/MAMUT-routing-TDVRPTW-Rifki2020/pull/3) at n=60: Duration improves from 18131 to 18040 seconds (91 seconds), with 9 routes. Re-validated the candidate and incumbent with mamut-routing-lib 0.12.0 under `td-fold/2`. Independent integer forward simulation over every integer departure in the full feasible depot interval confirms every route's minimum duration and earliest optimal departure. The file submitted in the pull request lacked its opening brace and a cost field, so the routes were stored again through the canonical improve-only TD store, with author and submission provenance. [Verification record](contributions/2026-11-13-pr-3.json).

Metadata-only change to the Rifki-10 BKS accepted on 2026-11-04: `metadata.authors` now lists the 3 members of the contributing team (Farah Abdallah, Leen Qawas, Bana Mohammad), at the contributors' request. Routes, cost and every other field are unchanged. The file's sha256 is now `24c5bde60429da528478e28d13da982e41a940990b8c2458e83259b5240228c9`, so the `stored_sha256` of the 2026-11-04 verification record refers to the previous bytes. The issue that carried the Rifki-10 submission (#2) is no longer reachable on GitHub because the submitting account was closed. The 2026-11-04 entry below, the BKS `source` field and the verification record keep its URL as a historical reference. The remaining 178 BKS are unchanged.

#### 2026-11-04

Accepted [Farah Abdallah's Rifki-10 contribution](https://github.com/ANR-MAMUT/MAMUT-routing-TDVRPTW-Rifki2020/issues/2) at n=60: Duration improves from 20187 to 20131 seconds (56 seconds), with 9 routes. Re-validated the candidate and incumbent with mamut-routing-lib 0.12.0 under `td-fold/2`. Independent integer forward simulation over every integer departure in the full feasible depot interval confirms every route's minimum duration and earliest optimal departure. Stored the candidate through the canonical improve-only TD store, with author and submission provenance. The remaining 179 BKS are unchanged. [Verification record](contributions/2026-11-04-issue-2.json).

#### 2026-09-24

**Re-priced under the `td-fold/2` checker contract (mamut-routing-lib 0.12.0), no route changed.** mamut-routing-lib 0.12.0 replaces the TD checker's route fold (checker contract `td-fold/1` -> `td-fold/2`). Waiting and service at a vertex are now applied exactly to the accumulated arrival times instead of being composed through a ratio interpolation, the departure window restricts the first arc without interpolation, and travel on slope-one pieces is computed by addition. Under `td-fold/1` a ready time could be off by an ulp, and on stepwise travel-time functions such an ulp could land past a step and read its upper branch. All 180 BKS were re-priced with `mamut-routing bks reprice-td`: 160 files were rewritten, 134 of them because the cost moved (by at most 2.8e-15 relative, float rounding only), the other 26 only to refresh `route_durations` / `route_departure_times` by ulps. Each BKS whose cost moved records `metadata.repriced` (previous cost, checker, contract, date). 46 optimality-stamped BKS moved (by at most 1.8e-11): their `proven_optimum` now equals the re-priced cost, and a `note` records that the proof was obtained under `td-fold/1`, that `dual_bound` is the prover's value under that arithmetic, and that re-certification under `td-fold/2` is pending.

#### 2026-08-06

**All 71 optimality certificates re-derived from scratch and re-stamped** under the 4-solve agreement protocol (cold and warm starts crossed with the 2 labeling modes, an audited exact-pricing phase in every run, 0 checker-infeasible priced columns) on Grid'5000, on the repaired kayros 1.5.1 build. The campaign was motivated by the 2026-08-05 withdrawal of 1 certificate in the Vu2020 TDVRPTW family and the subsequent finding that the certifying builds carried since-repaired pricing defects (documented in the kayros 1.5.1 release notes and CHANGELOG). Every re-issued stamp certifies bit-exactly the previously stored value. No solution data changed, and the stamps' provenance now cites this campaign and build.

#### 2026-07-18

**Family-wide re-validation on the exact value-jump solver path, no data changes.** Full-family sweep (all 180 instances, n=10 to n=60) on the kayros 1.1.0 release candidate (commit f8c788c), in which the exact value-jump (mollifier-free, tagged-vertical) labeling path is enabled automatically on stepwise travel-time functions and the near-vertical continuization of the retracted 2026-07-08 era is retired from the pipeline entirely. Protocol unchanged from the 2026-07-10/12 campaigns: 4 independent exact solves per instance (cold and warm starts crossed with the 2 labeling modes; time limits 600 s up to n=50, 3600 s at n=60), Grid'5000 reservation 6781269. Results: 720 runs, 0 checker-infeasible priced columns, and 0 disagreements with the stored data of any kind. 59 of the 71 certified BKS were independently re-certified at their stored proven optima (4-run agreement within 4e-12 of the stored cost). Every remaining run ended as an honest time-limit verdict, and no run anywhere contradicted a stored cost or certificate. The 22 instances of this family refused for checker-infeasible pricing by the 2026-07-10 wave (n=30 to n=60) now run clean: 0 such events in their 88 runs, uncertified only because the time limit intervened. Costs and certificates are untouched: this entry records that the family's certificates now stand re-validated on the production solver path.

#### 2026-07-12

**9 further BKS stamped proven optimal** by the weekend top-up of the exact re-certification campaign (2026-07-11/12, Grid'5000, about 3000 additional runs over the instances the 2026-07-10/11 campaign left open; time limit 1800 s per run). Protocol unchanged from the 2026-07-10/11 campaign: 4 independent exact solves (cold and warm starts crossed with the 2 labeling modes) agreeing on the value, an audited exact-pricing phase in every run, 0 checker-infeasible priced columns, and canonical-checker re-validation at stamping time. New certificates: Rifki-13, Rifki-18, Rifki-24 (n=20); Rifki-14, Rifki-19 (n=30); Rifki-9, Rifki-27, Rifki-30 (n=40); Rifki-20 (n=50). This family now carries 71 certified TDVRPTW BKS.

#### 2026-07-11

**62 BKS stamped proven optimal** (`metadata.optimality`): optimality certificates RETURN to this family after the 2026-07-08 retraction, under a strictly stronger protocol and a repaired prover (the pricing-ladder termination defect that produced the retracted certificates is fixed, and the 4-run protocol was validated to catch the failure class). Each stamp certifies 4 independent exact solves (cold and warm starts x 2 labeling modes) agreeing on the value, an audited exact-pricing phase in every run, 0 checker-infeasible priced columns, and canonical-checker re-validation at stamping time. Instances whose runs disagreed, timed out, or priced a checker-infeasible column carry NO stamp. All 30 instances at n=10 are certified. Larger sizes are certified only where all 4 runs closed and agreed.

#### 2026-07-10 (second entry)

3 further BKS improved under the same 4-run agreement protocol as the Vu2020 fold of the same date (exact solver, cold/warm x 2 labeling modes, checker re-validated, no optimality stamps): Rifki-26 n=10, Rifki-17 n=20, Rifki-17 n=50.

#### 2026-07-10

3 BKS improved by exact solves recorded as ordinary best-known solutions (no optimality stamps): Rifki-30 n=40 (15743 -> 15691, -0.33%), Rifki-20 n=50 (18888 -> 18810, -0.41%), Rifki-21 n=50 (16957 -> 16951, -0.04%), from a 2-arm re-certification attempt (kayros lera branch-price-and-cut, HiGHS backend, cold and warm runs in separate processes, TL 600 s, Grid'5000). The campaign's certificates were withheld: certification on this family's stepwise travel-time functions remains blocked because certified values were again warm-start-dependent on some instances, and additionally proved sensitive to the build toolchain (the same instance and input certify different "optima" under 2 compilers). Each folded solution was re-priced by the canonical checker before writing (checker cost authoritative). A checker-valid strict improvement stands regardless of certificate validity.

#### 2026-07-08

**Optimality certificates retracted (all 104 stamped BKS of this family) and 29 further BKS improved.** The head-to-head campaign below produced checker-valid solutions strictly below 29 of the 2026-07-07 "proven optima" (margins 1 to 136 duration units, up to 1.46%, e.g. Rifki-16 n=20: 8485 → 8361). Those certificates are refuted by counterexample. The failure was reproduced deterministically: the prover's certified value depends on its warm start (3 runs on Rifki-16 n=20 certified 8485, 8376 and 8361 as "optimal", each accepted as feasible by its own model), i.e. its pricing step is incomplete on this family's stepwise travel-time functions. The near-vertical segments used to continuize the step discontinuities for the solver's piecewise-linear machinery amplify its internal epsilon tolerances into route-level cost errors, so improving routes can be silently discarded and the dual bound is not sound. The canonical checker (exact arithmetic, no epsilons) remains authoritative and confirms every counterexample. All `metadata.optimality` stamps of this family are therefore retracted, including the 75 whose values were never contradicted (the campaign matched 66 of those values exactly without improving them, which supports the values but restores them to ordinary best-known status). The 29 counterexample solutions are now folded as regular BKS (mean -0.37%, largest -1.46%) through the standard improve-only, checker-authoritative pipeline. Re-certification will follow once the exact solver handles stepwise travel times exactly.

61 of 180 BKS improved (mean -0.62%, largest single improvement -1.78%) by a 20,808-run anytime-strategy head-to-head campaign on Grid'5000: kayros 0.4.0.dev0 (TD-ILS, TD-ACO+LS, and an ACO-then-ILS budget split, all over the granular time-dependent local search), per-size time limits (120 s for n<=30, 300 s for n<=60, 600 s for n<=100), seeds {42, 123, 456}, single-threaded runs. Improve-only fold: for each instance the campaign-best solution was re-priced by the canonical checker before writing (checker cost authoritative). Stored BKS marked proven optimal were left untouched.

#### 2026-07-07

13 BKS improved by exact solves and **proven optimal** by kayros 0.3.0 lera branch-price-and-cut (HiGHS backend, warm-started from the previous BKS, TL 600 s), from the certification campaign over all families n≤50. 3 n=10 entries were far from optimal (Rifki-26: 9045 → 4632, −48.8%; Rifki-25: −32.8%; Rifki-6: −26.5%). The other 10 range −0.1% to −2.5% across n=20..50. Certificates: optimal under checker-exact route costs and standard LP/pricing tolerances, completeness modulo Lera epsilon dominance. The same campaign certified 91 of the other stored TDVRPTW BKS of this family optimal as stored (27 of 30 at n=10, and the other 3 are among today's improvements).

Structured optimality metadata: all 104 BKS of this family proven optimal by that campaign now carry a machine-readable `metadata.optimality` object with the prover, certificate wording, proven optimum, dual bound and wall time (schema `OptimalityMetadata`, mamut-routing-lib ≥ 0.4.0). Additionally, **Rifki-1 (n=40) replaced**: the campaign proved the optimum 11961.999999999982 on a route set different from the stored 1 (cost 11961.999999999985, 3.6e-12 above). The difference is dust-level, but the checker-verified cost is authoritative, so the proven route set is now stored.

#### 2026-07-06

177 BKS improved by the first sweep of kayros 0.2.0.dev0 TD-ACO with time-dependent local search (tree-evaluated VND, every accepted move repriced by the checker-identical fold), 10 seeds per instance on Grid'5000.

#### 2026-07-05

179 BKS added, reaching full 180/180 coverage, from the initial large-scale seeding sweep across all 4 TD families run on 2026-07-04 (kayros 0.0.1 TD-ACO, Grid'5000, 10 seeds per instance, 13 520 runs total).

#### 2026-07-03

Family populated at the canonical K = 60 temporal granularity (180 instances + envelope-FIFO ATF sidecars) with 1 BKS, `Rifki29` (n=10), the single solution of a former heuristic BKS store still valid at K = 60, re-priced by the canonical checker.

## Former TDVRP repository

### Changelog of the Rifki2020 TDVRP BKS

All notable changes to the curated `Rifki2020` TDVRP best-known solutions (BKS) are recorded here. The objective is **Duration** (duration minimization), and the depot departure time of each route is a decision variable. Costs are the authoritative output of the canonical checker (`mamut_routing_lib.td.check_td_solution`): exact IEEE-754 double arithmetic, no epsilon thresholds, routes in canonical order (sorted by first customer), total summed in that order. Any strict improvement is therefore real. Reminder: vehicle attributes are the ones curated by Onyr (see README.md), and the TDVRP variant removes the time windows.

#### 2026-09-24

**Re-priced under the `td-fold/2` checker contract (mamut-routing-lib 0.12.0), no route changed.** mamut-routing-lib 0.12.0 replaces the TD checker's route fold (checker contract `td-fold/1` -> `td-fold/2`). Waiting and service at a vertex are now applied exactly to the accumulated arrival times instead of being composed through a ratio interpolation, the departure window restricts the first arc without interpolation, and travel on slope-one pieces is computed by addition. Under `td-fold/1` a ready time could be off by an ulp, and on stepwise travel-time functions such an ulp could land past a step and read its upper branch. All 180 BKS were re-priced with `mamut-routing bks reprice-td`: 146 files were rewritten, 36 of them because the cost moved (by at most 3.0e-03 relative), the other 110 only to refresh `route_durations` / `route_departure_times` by ulps. Each BKS whose cost moved records `metadata.repriced` (previous cost, checker, contract, date). Moves beyond float rounding: n=30 Rifki-30 8292 -> 8267; n=60 Rifki-13 17386 -> 17385. 9 optimality-stamped BKS moved (by at most 4.1e-12): their `proven_optimum` now equals the re-priced cost, and a `note` records that the proof was obtained under `td-fold/1`, that `dual_bound` is the prover's value under that arithmetic, and that re-certification under `td-fold/2` is pending.

#### 2026-08-06

**All 36 optimality certificates re-derived from scratch and re-stamped** under the 4-solve agreement protocol (cold and warm starts crossed with the 2 labeling modes, an audited exact-pricing phase in every run, 0 checker-infeasible priced columns) on Grid'5000, on the repaired kayros 1.5.1 build. The campaign was motivated by the 2026-08-05 withdrawal of 1 certificate in the Vu2020 TDVRPTW family and the subsequent finding that the certifying builds carried since-repaired pricing defects (documented in the kayros 1.5.1 release notes and CHANGELOG). Every re-issued stamp certifies bit-exactly the previously stored value. No solution data changed, and the stamps' provenance now cites this campaign and build.

#### 2026-07-18

**Family-wide re-validation on the exact value-jump solver path, no data changes.** Same sweep as the TDVRPTW twin family: all 180 instances (n=10 to n=60) on the kayros 1.1.0 release candidate (commit f8c788c), where the exact value-jump (mollifier-free, tagged-vertical) labeling path is enabled automatically on stepwise travel-time functions and the near-vertical continuization is retired entirely. 4 independent exact solves per instance (cold and warm starts crossed with the 2 labeling modes; time limits 600 s up to n=50, 3600 s at n=60), Grid'5000 reservation 6781269. Results: 720 runs, 0 checker-infeasible priced columns, 0 disagreements with the stored data. 33 of the 36 certified BKS were independently re-certified at their stored proven optima. Every remaining run ended as an honest resource verdict: at n=60, 66 runs were closed by the solver's graceful in-solver memory guard (11 GB per worker), the rest at the time limit. Costs and certificates are untouched.

#### 2026-07-12

**6 further BKS stamped proven optimal** by the weekend top-up of the exact re-certification campaign (2026-07-11/12, Grid'5000; time limit 1800 s per run). Protocol unchanged from the 2026-07-10/11 campaign: 4 independent exact solves (cold and warm starts crossed with the 2 labeling modes) agreeing on the value, an audited exact-pricing phase in every run, 0 checker-infeasible priced columns, and canonical-checker re-validation at stamping time. New certificates: Rifki-3, Rifki-23 (n=10); Rifki-11, Rifki-19, Rifki-24, Rifki-30 (n=20). This family now carries 36 certified TDVRP BKS.

#### 2026-07-11 (second entry)

1 further BKS stamped proven optimal (Rifki-3 n=10): certified by the exact value-jump solver path (mollifier-free), cold and warm solves agreeing, exact-pricing phase audited, where the standard path had timed out. The exact path was validated family-wide the same day: 77/77 agreement with existing certificates and a near-total elimination of checker-infeasible pricing events (1 in 720 runs against 68 before).

#### 2026-07-11

**30 BKS stamped proven optimal** (`metadata.optimality`), the TDVRP twin's first certificates (all of n=10): optimality certificates return to this family after the 2026-07-08 retraction, under the 4-run agreement protocol and a repaired prover. Each stamp certifies 4 independent exact solves (cold and warm starts x 2 labeling modes) agreeing on the value, an audited exact-pricing phase in every run, 0 checker-infeasible priced columns, and canonical-checker re-validation at stamping time. Instances whose runs disagreed, timed out, or priced a checker-infeasible column carry NO stamp.

#### 2026-07-08

**Optimality certificates retracted (all 56 stamped BKS of this family) and 14 further BKS improved.** The head-to-head campaign below produced checker-valid solutions strictly below 14 of the 2026-07-07 "proven optima" (margins 4 to 73 duration units, up to 0.51%). Those certificates are refuted by counterexample. The failure was reproduced deterministically on the TDVRPTW twin family: the prover's certified value depends on its warm start (3 runs on 1 instance certified 3 different values as "optimal"), i.e. its pricing step is incomplete on this family's stepwise travel-time functions. The near-vertical segments used to continuize the step discontinuities for the solver's piecewise-linear machinery amplify its internal epsilon tolerances into route-level cost errors, so improving routes can be silently discarded and the dual bound is not sound. The canonical checker (exact arithmetic, no epsilons) remains authoritative and confirms every counterexample. All `metadata.optimality` stamps of this family are therefore retracted, including the 42 whose values were never contradicted (the campaign matched all 42 exactly without improving them, which supports the values but restores them to ordinary best-known status). The 14 counterexample solutions are now folded as regular BKS (mean -0.23%, largest -0.51%) through the standard improve-only, checker-authoritative pipeline. Re-certification will follow once the exact solver handles stepwise travel times exactly.

67 of 180 BKS improved (mean -0.44%, largest single improvement -1.64%) by a 20,808-run anytime-strategy head-to-head campaign on Grid'5000: kayros 0.4.0.dev0 (TD-ILS, TD-ACO+LS, and an ACO-then-ILS budget split, all over the granular time-dependent local search), per-size time limits (120 s for n<=30, 300 s for n<=60, 600 s for n<=100), seeds {42, 123, 456}, single-threaded runs. Improve-only fold: for each instance the campaign-best solution was re-priced by the canonical checker before writing (checker cost authoritative). Stored BKS marked proven optimal were left untouched.

#### 2026-07-07

1 BKS improved by an exact solve and **proven optimal**: Rifki-23 n=20 (5951 → 5950), by kayros 0.3.0 lera branch-price-and-cut (HiGHS backend, warm-started from the previous BKS, TL 600 s), from the certification campaign over all families n≤50. The same campaign certified 55 of the other stored TDVRP BKS of this family optimal as stored (all 30 at n=10).

Structured optimality metadata: all 56 BKS of this family proven optimal by that campaign now carry a machine-readable `metadata.optimality` object with the prover, certificate wording, proven optimum, dual bound and wall time (schema `OptimalityMetadata`, mamut-routing-lib ≥ 0.4.0).

#### 2026-07-06

177 BKS improved by the first sweep of kayros 0.2.0.dev0 TD-ACO with time-dependent local search (tree-evaluated VND, every accepted move repriced by the checker-identical fold), 10 seeds per instance on Grid'5000.

#### 2026-07-05

Initial BKS population, 180/180: the TDVRP variant had no earlier heuristic solutions, so all BKS come from the initial large-scale seeding sweep across all 4 TD families run on 2026-07-04 (kayros 0.0.1 TD-ACO, Grid'5000, 10 seeds per instance, 13 520 runs total).

#### 2026-07-03

Family populated at the canonical K = 60 temporal granularity (180 instances + envelope-FIFO ATF sidecars), no BKS yet.
