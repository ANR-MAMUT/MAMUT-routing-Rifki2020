# Rifki2020 benchmark family (TDVRPTW and TDVRP, real Lyon road-network travel times)

Benchmark family repository of [MAMUT-routing](https://github.com/ANR-MAMUT/MAMUT-routing) (ANR MAMUT, ANR-22-CE22-0016), mounted there as `benchmarks/Rifki2020`. It holds the 2 problem variants of the family, TDVRPTW and its time-window-free twin TDVRP. Self-contained: instances, canonical arrival-time-function (ATF) sidecars and best-known solutions ship together.

Canonical Duration-Minimization TDVRPTW instances built from the real-world time-dependent travel times of Rifki, Chiabaut & Solnon (2020). These are shortest travel times computed on the Lyon road network from a realistic traffic simulation, at ideal spatial granularity (σ = 100) and 12-minute temporal granularity (K = 60 steps of 720 s over a 12 h horizon, see the granularity section below). 6 sizes (10–60 customers) × 30 instances = 180 instances, named `Rifki-<id>` under `n=<customers>`.

## The TDVRP variant

The TDVRP variant holds the same 180 Duration-Minimization instances (6 sizes, 10–60 customers, × 30 ids, named `Rifki-<id>`) with the **time windows removed**. Capacity, fleet size, service times, demands, coordinates and the time-dependent travel times are identical, and the 2 variants share the same ATF sidecars under `sidecars/`. Everything else documented below applies verbatim to both variants: the raw source (Rifki, Chiabaut & Solnon 2020 Lyon data, σ = 100, K = 60 steps of 720 s), the choice of the K = 60 granularity over the finest K = 120, the arrival-time lower-envelope FIFO restoration (exact MD92, NDCPWLF vertical steps at surviving upward jumps), the deliberate replacement of the original [RCS20] time-window files by attributes generated once by a former generation pipeline, the deterministic stress-layout coordinates, the all-gzipped sidecar storage, and the "checker defines the objective" rule. Per-instance provenance lives in each instance's `metadata`.

## Layout

- `TDVRPTW/n=<customers>/` and `TDVRP/n=<customers>/` hold the instances (`Rifki-<id>.vrp.json`) and their best-known solutions (`Rifki-<id>.bks.Duration.json`) of each variant, for example `TDVRPTW/n=10/Rifki-10.vrp.json` and `TDVRP/n=10/Rifki-10.bks.Duration.json`.
- `sidecars/n=<customers>/` holds the ATF sidecars, stored once and shared by the 2 variants, for example `sidecars/n=10/Rifki-10.atf.json.gz`. Each instance references its sidecar by a path relative to the repository root, with the SHA-256 of the sidecar content.
- `contributions/` holds the verification records of accepted community contributions of best-known solutions, for example `contributions/2026-11-13-pr-3.json`.
- `mamut-collection.json` at the root marks the repository as a family collection (`"layout_version": 2`).
- `history/` holds the commit maps of the former repositories (see the history section below).

## Format

Each instance has 3 artifacts:

- `Rifki-<id>.vrp.json`: instance data in the MAMUT shape, with depot index 0 (no duplicated end depot), mandatory display `coordinates`, `horizon` `[0, 43200]`, and a `td` block referencing the ATF sidecar with its storage-independent sha256.
- `Rifki-<id>.atf.json.gz`: the canonical ground truth, one arrival-time NDCPWLF per arc of the complete graph. All sidecars are gzipped in this repository, and the sha256 covers the uncompressed canonical bytes.
- `Rifki-<id>.bks.Duration.json`: best known solution under the `Duration` objective. Costs are always the authoritative output of the canonical checker (`mamut_routing_lib.td.check_td_solution`).

## FIFO restoration (the defining preprocessing of this family)

The raw data gives piecewise *constant* travel times per arc (integer seconds per 720 s step), which violate the FIFO property at every boundary where travel time decreases. The canonical ATFs apply the **arrival-time lower envelope**, the greatest non-decreasing minorant of the raw arrival function, `α̂(t) = min_{t' ≥ t} (t' + τ(κ(t')))`. This is the exact form of the classic MD92 transformation (Malandraki & Daskin 1992). Decreasing steps get a slope −1 descent placed entirely before the boundary. Upward jumps are kept and encoded as **vertical steps** of the NDCPWLF (duplicate `xs`, and evaluation at a step returns the smallest value, which is the envelope's own value there). The transformation is parameter-free, exact on the integer raw data, and changes values only on descent tails where FIFO restoration is mathematically unavoidable.

## Why K = 60 (and not the finest K = 120)

The original distribution ships **5 temporal granularities** of the same underlying Lyon simulation (step lengths l ∈ {720, 60, 24, 12, 6} minutes, i.e. K ∈ {1, 12, 30, 60, 120} steps). This family pins **K = 60** (l = 12 min), for deliberate reasons:

- **Breakpoint weight.** After FIFO restoration, K = 120 yields ≈ 238 breakpoints per arc versus ≈ 119 at K = 60, and sidecar sizes halve accordingly. At K = 60 the family sits in the same per-arc weight class as the other canonical TD families (Ari2018/Vu2020 ≈ 150 breakpoints per arc, Dabia2013 ≈ 10) instead of being a 2× outlier. Composition-based solver operations scale linearly in breakpoint counts.
- **K = 60 is not a lossy compression of K = 120.** The granularities are independent published aggregations of the same simulation, not derivable from one another (at 96% of step boundaries the l = 12 value matches neither the first, the min, nor the mean of the 2 corresponding l = 6 values, and envelope travel times differ by ~12% on average between the 2). Choosing K = 60 selects one published discretization of the real data, every bit as authentic as the finest one.
- **Nothing was invalidated.** The granularity was fixed while the canonical family had no best-known solutions, so no published result depends on the earlier K = 120 draft.

## Attribute provenance (deliberately not the original [RCS20] time windows)

The original benchmark distributes separate time-window files (`timewindow_<n>_<k>_<w>.txt`, horizon split into 2/4/6 disjoint slots). This family deliberately does **not** use them, for 3 reasons. They are degenerate for benchmarking purposes (many windows clustered near the horizon start, many identical), original service times are uniformly 180 s, and no demands or fleet data exist (the original problem is not capacitated). Instead, the time windows, gaussian service times, demands, vehicle capacities and fleet sizes are the values generated once by a former generation pipeline (2024), pinned here verbatim. Every instance's `metadata.legacy_attribute_source` names the exact legacy file. The TDVRP variant uses the same attributes without the time windows. The original TW files remain available upstream for anyone studying the [RCS20] setting.

Display coordinates are not part of the raw data (real road network). They are a deterministic stress-layout embedding (SMACOF, classical-MDS init) of the time-averaged travel-time matrix. Per-instance embedding quality (Kruskal stress-1 ≈ 0.05–0.12, Pearson r ≈ 0.99) is recorded in `metadata.coordinates_method`. Coordinates are informative-only. The checker never reads them.

Population pipeline: `populate_td_rifki2020` v2 (curation tooling maintained outside this repository, links will be published with the benchmark paper). Every artifact's `generator` block records the pipeline name and version.

## Library compatibility

This layout is read by `mamut-routing-lib` 0.13 and later, which is in development and not released yet. `mamut-routing-lib` 0.12 and earlier read the former layout, which stays available read-only in the archived former repositories [MAMUT-routing-TDVRP-Rifki2020](https://github.com/ANR-MAMUT/MAMUT-routing-TDVRP-Rifki2020) and [MAMUT-routing-TDVRPTW-Rifki2020](https://github.com/ANR-MAMUT/MAMUT-routing-TDVRPTW-Rifki2020).

## Provenance and licensing

Underlying travel-time data: Rifki, Chiabaut & Solnon (2020), Lyon road network with a traffic simulation built from real-world data (see the TDGPDP page of Christine Solnon). MAMUT-authored curation artifacts are distributed under the MIT license (see [LICENSE](LICENSE)). This notice does not relicense the underlying third-party data.

## Citation and benchmark authorship

The authors, in citation order, are Adrien Pichon, Florian Rascoussier, Romain Billot, Lina Fahed, Alexandru-Liviu Olteanu, Marc Sevaux and Christine Solnon. The 2 lead authors are ordered alphabetically by surname, followed by the scientific supervisors in alphabetical order. Their ORCID identifiers are recorded in [CITATION.cff](CITATION.cff).

The listed authors contribute the TDVRPTW and TDVRP benchmarks constructed from the Lyon travel-time data of Rifki, Chiabaut and Solnon (2020). The contribution includes selecting the 12-minute discretization, applying and validating the arrival-time lower-envelope FIFO transformation, generating the routing attributes and publishing the resulting instances. In the TDVRPTW variant, the generated time windows, demands, capacities, fleet sizes and service times define a capacitated multi-vehicle benchmark distinct from the source study. The time-window-free TDVRP variant preserves the demands, capacities, fleet sizes, service times and arrival-time functions of the TDVRPTW twin for paired comparisons. Cite this repository for the benchmark adaptation, Rifki, Chiabaut and Solnon for the underlying data, and Malandraki and Daskin (1992) for the FIFO-restoration method. Individual solution-contributor credits are recorded with the solutions. Record the exact Git commit used in experiments.

## History of this repository

Until November 2026 the 2 variants lived in 2 repositories, `MAMUT-routing-TDVRPTW-Rifki2020` and `MAMUT-routing-TDVRP-Rifki2020`. Their histories are imported here. Each history was rewritten under the directory of its problem type (`TDVRPTW/` or `TDVRP/`), and a merge commit joins the 2. The rewrite gave every former commit a new identifier. `history/` holds 1 map per former repository, `history/commit-map-MAMUT-routing-TDVRPTW-Rifki2020.tsv` and `history/commit-map-MAMUT-routing-TDVRP-Rifki2020.tsv`. Each map is tab-separated, with the former commit identifier and the new one on each line, after a header line. Links to issues and pull requests of the former repositories, in [CHANGELOG.md](CHANGELOG.md) and in `contributions/`, are kept as historical references. See [CHANGELOG.md](CHANGELOG.md) for the reorganisation.
