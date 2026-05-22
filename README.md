# MPT Project — Singapore human mobility & radiation model

Geospatial notebooks for Singapore planning-area/subzone workflows: resident counts, GPR-based population proxies (Gross Plot Ratio, not Gaussian Process Regression), centroid distances, transit OD flux, spatial gridding workflows (`SG_GPR.ipynb`, `GPR_LU.ipynb`), radiation-model predictions (`pre_gpr_*`), and evaluation/plots.

## Layout

| Path | Role |
|------|------|
| `docs/README.ipynb` | Full narrative/math documentation (converted complement to this file) |
| `notebooks/01_preprocess/` | Resident tables, GPR preprocessing (`pre_residents`, `pre_gpr_*`) |
| `notebooks/02_zones_centroids/` | PA/SZ boundaries, centroids, distance matrices (`pa&sz_centroids`, `residents_by_sz&pa`) |
| `notebooks/03_population_flux/` | Bus/MRT OD to PA flux (`pop_flux_*`, `pop_viaFlux`) |
| `notebooks/04_gpr_modeling/` | GPR land use & gridding (`GPR_LU`, `SG_GPR`) |
| `notebooks/05_evaluation/` | Model vs flux (`Evaluation_*`) |
| `notebooks/06_visualization/` | Figures (`Plot`, `Plots`) |
| `data/raw/` | Original downloads (government shapefiles/CSVs): place or symlink datasets here |
| `data/processed/` | Cleaned / merged tables (optional split; see below) |
| `data/external/` | Third-party or static reference data |
| `outputs/figures/` | Exported figures |
| `outputs/models/` | Saved model artifacts |
| `configs/` | Parameters, small YAML/JSON (optional) |
| `src/mpt/` | Shared Python package (extract notebook code here as needed) |

## Suggested workflow order

1. **Zones & residents** — `02_zones_centroids/residents_by_sz&pa.ipynb` (population by PA/SZ); `01_preprocess/pre_residents.ipynb` for radiation inputs.  
2. **Centroids & distances** — `02_zones_centroids/pa&sz_centroids.ipynb` → `distance_pa_to_pa.csv`, `dist_pa_to_sz.csv`, etc.  
3. **Flux** — `03_population_flux/pop_flux_2019.ipynb` / `pop_flux_2020.ipynb` then `pop_viaFlux.ipynb` as needed.  
4. **GPR pipeline** — `04_gpr_modeling/GPR_LU.ipynb` → `SG_GPR.ipynb` (outputs e.g. `SG_GPR_sorted.geojson`, `GPR_pop.csv`).  
5. **Predictions** — `01_preprocess/pre_gpr_2019.ipynb` / `pre_gpr_2020.ipynb`.  
6. **Evaluation & plots** — `05_evaluation/Evaluation_*.ipynb`, `06_visualization/Plot.ipynb`, `06_visualization/Plots.ipynb`.

## Data path in notebooks

Notebooks resolve `path` as `<repo root>/data` by searching upward for `README.md`. Prefer **starting Jupyter’s working directory at the repository root** (or “Open folder” / root in JupyterLab).

Place inputs and intermediate CSV/GeoJSON that notebooks read/write under `data/` (for example symlink your existing “data set” folder to `data/`, or copy boundary folders and OD files into `data/raw/` and mirror outputs under `data/processed/`).

## Environment

```bash
python -m venv .venv
source .venv/bin/activate   # Windows: .venv\Scripts\activate
pip install -r requirements.txt
```

Use a Geo stack that matches notebook imports (e.g. `geopandas`, `matplotlib`); kernels differ by machine — see notebook metadata.
