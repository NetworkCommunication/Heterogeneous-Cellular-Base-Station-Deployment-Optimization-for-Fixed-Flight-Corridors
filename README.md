# Heterogeneous-Cellular-Base-Station-Deployment-Optimization-for-Fixed-Flight-Corridors
# AirHERO — Paper Reproduction Code

Public release v1.0 · 2026-10-28

This package accompanies **Heterogeneous Cellular Base Station Deployment Optimization for Fixed Flight Corridors**. It contains the preserved implementation, fixed experimental inputs, configurations, per-seed results, metric inputs and the verified 60-corridor supplement.

**Validation scope:** the package passes lightweight execution, integrity and historical-result checks. Complete paper experiments were **not** rerun during packaging. “Reproduction code” describes the supplied runnable materials; it is not a claim that every paper result has been independently reproduced.

The publisher confirmed authorization to publicly distribute the fixed POI and derived-route data. Generated/proxy corridors are not measured UAV trajectories, and simulated existing stations are not operator records. See `DATA.md`, `THIRD_PARTY_NOTICES.md` and `LICENSE_STATUS.md`. No new project-wide open-source license is assigned.

## Download and extract

[Download AirHERO v1.0 ZIP (12.48 MiB)]

This repository distributes the reproduction code as a ZIP archive. Download and
extract it, then open the extracted `AirHERO` directory before running the
commands below. Cloning this repository alone does not extract the code.

```powershell
cd AirHERO
```

The documentation files mentioned below, including `DATA.md`, `VALIDATION.md`,
and `MANIFEST.csv`, are included inside the downloaded archive.

ZIP SHA-256:

```text
83b21bc62eec4981369c4875552e44a64d7be483706bdbe855eb09750c24d695
```

## Quick start

Tested with Python 3.11.4 on Windows. Use a fresh environment if installing dependencies; the listed versions describe the tested environment, not a clean-install-tested lockfile. See `DEPENDENCIES.md`.

```powershell
python -m pip install -r requirements-verified.txt
python -B release_tools/verify_bundle.py
python -B release_tools/run_release.py --config configs/release/smoke.json --output artifacts/my_smoke
python -B -m pytest -q -p no:cacheprovider tests
```

Run from the extracted package root. The launchers locate their own root and need no original project path, symlink or preset AIRHERO/PYTHONPATH variable. Choose a short extraction path on Windows; `release_tools/extract_bundle.py` supports long filenames.

The smoke keeps all **144 stations and 80 corridors / 2,400 samples**, with 8 particles, 3 iterations per stage and seed 2026. It runs both stages and exports selected deployments, including 5GA directions, objectives, ASU, coverage and continuity metrics. It is a pipeline check, not a performance experiment; it is too short to exercise the long-run stagnation response.

## Paper-scale runs and historical checks

These commands use the preserved algorithms and a new output directory. They can take substantially longer than the smoke. Avoid executing original integration scripts directly against archived result folders, because their original resume/aggregation behavior can update those files.

```powershell
# Main all-K AirHERO: 50 particles, 150 iterations per stage, seeds 2026/2027/2028
python -B release_tools/run_release.py --config configs/release/paper_entry.json --output artifacts/new_main_run

# One baseline / seed; method keys and other entry points are in PAPER_CODE_MAP.md
python -B release_tools/run_historical.py baseline --method nsga2_current_then_future_asu_v2 --seed 2026 --output artifacts/new_nsga2_2026
python -B release_tools/run_historical.py ablation --method without_closed_loop_elite_immigration_response --seed 2026 --output artifacts/new_ablation_2026
python -B release_tools/run_historical.py sensitivity --route-count 40 --k 10 --output artifacts/new_sensitivity_40_K10
python -B release_tools/run_historical.py dense --seed 2026 --output artifacts/new_dense_2026
python -B release_tools/run_historical.py search --method airhero_search_same_initialization --seed 2026 --output artifacts/new_search_2026

# Read-only historical arithmetic / HV-GD-IGD reevaluation; no optimization
python -B release_tools/recheck_history.py
python -B release_tools/recheck_fronts.py
python -B release_tools/plot_history.py

# 60-corridor supplement: default is input check only
python -B release_tools/run_route60.py --output artifacts/my_route60_check
# Explicit formal-scale rerun of one supplementary unit
python -B release_tools/run_route60.py --run --k 10 --output artifacts/new_route60_K10
```

The main saved sensitivity grid is 20/40/50/80 corridors. The actual 60-corridor supplement uses **25 current + 35 predicted corridors**, seed 2026. Both AirHERO paths refine all K stations. Their results remain separately identified: the 60-corridor/K10 ASU is 0.13486374562466252; 0.1491944857460591 belongs to the main 50-corridor/K10 cell. See `ROUTE60_SUPPLEMENT.md`.

## Package contents and provenance

- `src/`, `scripts/`: unchanged research source and required import dependencies; existing baseline modules remain isolated.
- `configs/release/`: separate smoke and paper-entry configurations. Original historical snapshots remain beside results; much of the original effective configuration is built in Python.
- `data/`, `artifacts/tmc_rebuild/`: fixed inputs, per-seed results, deployments, objectives, runtimes, statistics and plotting data.
- `release_tools/`: small portable launchers, historical checks, plotting and archive verification tools.
- `PAPER_CODE_MAP.md`: entry → dependencies → data/config → results → statistics/figures.
- `VALIDATION.md`: executed checks, timings, environment and unverified scope.
- `MANIFEST.csv`: repository-relative origins, source/release SHA-256 and packaging changes. It contains no publisher machine paths.

The original algorithm, evaluator, metric computations and historical numeric results were not changed for publication. The publisher accepted the documented implementation conventions in `IMPLEMENTATION_NOTES.md`; this archive reports actual behavior instead of changing results to match text.
The original province-scale GeoPackage is not distributed. Frozen inputs suffice for the supplied numerical runs; rebuilding the original city GIS extraction requires the original external dataset. Thus this is self-contained for the supplied numerical checks, not for complete GIS reconstruction. The associated paper should be obtained separately in its publisher revision.
