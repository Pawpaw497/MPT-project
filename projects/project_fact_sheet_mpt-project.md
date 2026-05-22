# Project Fact Sheet

## What this project actually does

**Implemented:** A Jupyter-notebook research pipeline that applies the **radiation model** of human mobility to Singapore, predicting origin–destination trip flux between **55 planning areas (PAs)** and comparing predictions to observed public-transit flows. The workflow is documented in `README.ipynb` and spans population preparation, geospatial distance/`s_ij` computation, bus/MRT flux aggregation, model fitting, and evaluation for **2019 and 2020**.

Primary outcomes (from notebook outputs and saved CSV references, not shipped in-repo):
- PA×PA predicted flux matrices (e.g. `Prediction2019_gpr.csv`, `Prediction2020_gpr.csv`, `Prediction_i2j_use_Residents.csv` — referenced in `pre_gpr_*.ipynb`, `Evaluation_*.ipynb`)
- Aggregated transit flux by PA (e.g. `Total_flux_ij_2019.csv`, `Total_flux_ij_2020.csv` — written in `pop_flux_2019.ipynb`, `pop_flux_2020.ipynb`)
- Evaluation plots: log-scale heatmaps, relative-error maps, land-use–stratified metrics (`Evaluation_2019.ipynb`, `Evaluation_2020.ipynb`, `Plot.ipynb`, `Plots.ipynb`)

**Inferred pattern:** Coursework/thesis work for **PC5287 (M.Sc. Physics and Technology)** — local data paths in notebooks point to a personal `data set` folder under that course name; not stated as a product or service.

**Who it serves:** Research/analysis audience (the author and reviewers); no user-facing application evidenced in-repo.

## Key engineering challenges

- **Geospatial aggregation at multiple scales:** Subzone resident counts, PA-level totals, and finer **Gross Plot Ratio (GPR)** grid polygons must align with URA Master Plan 2019 boundaries (`residents_by_sz&pa.ipynb`, `SG_GPR.ipynb`, `GPR_LU.ipynb`, `pa&sz_centroids.ipynb`).
- **Computing `s_ij` via geometry:** Population within a circle of radius `r_ij` is approximated by counting subzones (or GPR cells) whose **centroids** fall inside the circle (`README.ipynb`, `pa&sz_centroids.ipynb`, `SG_GPR.ipynb`).
- **Transit OD → PA flux:** Bus and MRT stop/station origin–destination volumes are joined to locations and rolled up to PA pairs, with manual fixes for renamed/missing stations and zero-flux areas (`pop_flux_2019.ipynb`, `pop_flux_2020.ipynb`; cleaning notes in `README.ipynb`).
- **Two population proxies:** Resident census totals vs. GPR-based population density, including land-use classification (BUSINESS / COMMERCIAL / RESIDENTIAL) for stratified evaluation (`pre_residents.ipynb`, `pre_gpr_2019.ipynb`, `pre_gpr_2020.ipynb`, `GPR_LU.ipynb`).
- **Missing and sparse flux data:** KNN imputation and linear regression used in evaluation when comparing predicted vs. observed matrices (`Evaluation_2019.ipynb`, `Evaluation_2020.ipynb`).
- **Performance:** Author notes slow execution when using planning-area **name strings** as keys (`README.ipynb`); notebooks include timing/debug traces (e.g. `pop_viaFlux.ipynb`).

## Technologies directly used

| Technology | Evidence |
|------------|----------|
| **Python 3** (3.10 in execution traces) | Notebook kernelspec `conda-env-geo_env-py`; traceback paths show `python3.10` |
| **Jupyter notebooks** | 15 root `.ipynb` files; `README.ipynb` as project doc |
| **pandas / NumPy** | Imports across all pipeline notebooks |
| **GeoPandas, Shapely, Fiona** | `pa&sz_centroids.ipynb`, `pop_flux_*.ipynb`, `SG_GPR.ipynb`, `GPR_LU.ipynb` |
| **pyproj** | CRS handling in `pa&sz_centroids.ipynb` |
| **Matplotlib** (incl. 3D) | Plotting in `Plot.ipynb`, `Evaluation_*.ipynb`, `pre_gpr_*.ipynb` |
| **scikit-learn** | `r2_score`, `mean_absolute_percentage_error`, `explained_variance_score`, `KNNImputer`, `LinearRegression` in `Evaluation_*.ipynb` |
| **Git** | `.git/` history Sep 2022 – Jun 2023 |

**Not evidenced in repo:** `requirements.txt`, `pyproject.toml`, `environment.yml`, or pinned dependency versions. The checked-in `.venv/` contains only virtualenv stubs (no installed packages).

## Technologies indirectly involved

- **Conda environment `geo_env`** — referenced in notebook kernelspec metadata and Anaconda paths in error output.
- **External geodata formats:** KML, GeoJSON, CSV, JSON (`gpd.read_file`, `pd.read_csv`, `pd.read_json` in notebooks).
- **Cloud/local filesystem paths:** iCloud Drive and Google Drive absolute paths hard-coded for the external `data set` directory (not portable without path changes).
- **`.ipynb_checkpoints/`** — Jupyter auto-save artifacts committed alongside notebooks.

**Not evidenced:** Docker, cloud deploy configs, CI (`.github/workflows` absent), databases, message queues, web servers, or test runners.

## Architecture patterns

- **Notebook pipeline / exploratory research workflow** — each stage is a standalone notebook with sequential data prep → model → evaluation → plotting.
- **No packaged application layer** — no `src/`, Python modules, CLI entrypoints, or importable library structure.
- **File-based data interchange** — intermediate and final results written as CSV/PNG to an external data directory referenced by `path` variables.
- **Dual-year, dual-population-variant design** — parallel 2019/2020 notebooks and resident vs. GPR prediction paths (`pre_residents.ipynb` vs. `pre_gpr_2019.ipynb` / `pre_gpr_2020.ipynb`).

## Complexity signals

- **~15 primary notebooks**, ~36k total lines of notebook JSON (includes embedded outputs).
- **55×55 PA matrices** for predictions and flux (`README.ipynb`).
- **Multi-source geospatial joins:** URA planning-area/subzone boundaries, GPR polygons, bus/MRT locations and OD tables.
- **Land-use stratification** across three categories in evaluation (`Evaluation_2019.ipynb`, `Evaluation_2020.ipynb`, `GPR_LU.ipynb`).
- **Alternative model variant** using flux-derived `m_i` and `s_ij` (`pop_viaFlux.ipynb`).
- **No automated tests, lint config, or CI** visible in the repository tree.

## Production-relevant aspects

| Aspect | Status |
|--------|--------|
| Deployment model | **Not evidenced** — local/offline notebook execution only |
| Data in repository | **Not included** — all inputs referenced via absolute local/cloud paths |
| Secrets / config management | **Not evidenced** — hard-coded `path` strings per notebook |
| Reproducibility | **Partial** — git history and README describe stages, but no dependency lockfile or data manifest |
| Observability / logging | **Not evidenced** — print/plot outputs in notebooks only |
| Migrations / schema | **N/A** — CSV-centric, no database |

**Possible production implication (hypothetical):** The geospatial aggregation and OD-to-zone rollup patterns could inform an urban mobility analytics prototype, but no such system exists in this repo.

## Resume-safe claims

- Built an end-to-end **radiation-model mobility study** for Singapore planning areas, documented in `README.ipynb` with explicit math for `T_ij`, `m_i`, `n_j`, and `s_ij`.
- Processed **government resident statistics** and **URA Master Plan 2019** boundary data to derive PA/subzone centroids and distance matrices (`residents_by_sz&pa.ipynb`, `pa&sz_centroids.ipynb`).
- Integrated **bus and MRT origin–destination volumes** with stop/station geolocation to produce PA-level flux matrices for 2019 and 2020 (`pop_flux_2019.ipynb`, `pop_flux_2020.ipynb`).
- Implemented a **GPR (Gross Plot Ratio)–based population proxy** at finer spatial resolution than administrative units, including land-use grouping (`SG_GPR.ipynb`, `GPR_LU.ipynb`, `pre_gpr_*.ipynb`).
- Evaluated model fit using **R², explained variance, MAPE**, log heatmaps, relative-error maps, and land-use–stratified comparisons (`Evaluation_2019.ipynb`, `Evaluation_2020.ipynb`).
- Handled real-world data quality issues: missing planning areas, zero-flux zones, inconsistent MRT station naming (`README.ipynb`, `pop_flux_*.ipynb`).

## Claims that should NOT be made

- **Production system, API, or deployed service** — no server code, containers, or deploy configs.
- **Large-scale / high-traffic engineering** — no performance benchmarks, infra, or scale metrics in-repo.
- **Deep learning or custom ML model training** — core prediction is the analytic radiation formula; sklearn is used for metrics, imputation, and simple linear regression in evaluation.
- **Real-time mobility tracking or operational transit analytics** — batch historical analysis on 2019/2020 datasets.
- **Data ownership or open dataset publication** — raw CSV/GeoJSON inputs are external and not versioned here.
- **Team leadership, CI/CD, or platform ownership** — solo notebook workflow; no CI or collaboration tooling evidenced.
- **GPR as Gaussian Process Regression** — in this project GPR means **Gross Plot Ratio** (urban planning metric), per `README.ipynb` and `SG_GPR.ipynb`.

## Possible target roles

- **Geospatial / GIS data analyst** — GeoPandas, boundary joins, centroid/distance computation, map visualization.
- **Urban mobility / transportation research analyst** — OD matrix construction, gravity/radiation-type spatial interaction models.
- **Applied data scientist (research/academic)** — hypothesis-driven pipeline, metric design, exploratory evaluation notebooks.
- **Computational social science / spatial economics researcher** — population–flow modeling with administrative and land-use covariates.
- **Quantitative analyst (entry to mid, research-oriented)** — pandas-centric ETL, matrix modeling, matplotlib reporting; less aligned with backend/platform or MLOps roles unless framed as research coding experience.
