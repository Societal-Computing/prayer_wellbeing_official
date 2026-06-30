# Does Prayer Improve Well-Being?

This repository helps to replicate results from the paper "Does Prayer Improve Well-Being? Triangulating the World Values Survey with Real-Time Social Media Polling" by Till Koebe, Mohammad Khaled Fares Moslemany, Ingmar Weber and Valentina Rotondi.

This repo reproduces the analyses, figures, and tables in the manuscript.

The study combines two data sources: (i) World Values Survey (WVS) and (ii) Instagram video-poll advertising campaign run by the authors across 15 African locations.

## Repository structure

```
.
├── src/                         Analysis notebooks (run from this directory)
│   ├── did_analysis.ipynb       Main difference-in-differences analysis; figures; LaTeX tables
│   ├── specification_curve.ipynb  96-cell specification curve
│   ├── wild_bootstrap.ipynb     Wild cluster bootstrap-t inference for the two Instagram DIDs
│   ├── prepare_covariates.ipynb Builds midsave/control_vars.parquet (needs external data; provenance)
│   └── select_target_areas.ipynb  How the 15 ad locations were chosen (needs external data; provenance)
├── input/                       Source data (Instagram campaign exports; non-identifiable, aggregate)
├── midsave/                     Intermediate datasets (provided, so you can skip the external-data step)
├── output/                      Generated figures, tables, and result CSVs (created when you run)
├── wvs/                         World Values Survey analysis (Stata) — see wvs/README.md
└── requirements.txt             Python dependencies for the core analysis
```

## Data

- **Instagram campaign data** (`input/`, `midsave/`): collected by the authors via Meta's advertising
  platform. Meta returns only **aggregate** response counts per advertisement together with reach and
  demographic-stratum metadata; no individual respondent can be identified. These files are included.
- **World Values Survey**: we use the WVS Trends 3.0 file, which we are not permitted to redistribute.
  Download it from <https://www.worldvaluessurvey.org/> and follow `wvs/README.md`.
- **External covariates** (used only by `prepare_covariates.ipynb`): subnational deprivation (SEDAC GRDI),
  population (WorldPop), and the Digital Gender Gaps project (Breen et al., 2025). We do not redistribute
  these; instead we provide the resulting `midsave/control_vars.parquet`, so the main analysis runs
  without them. In the notebooks these external paths appear as `/path/to/external_data/...` placeholders.

## Software

Python 3.10+ recommended. Install the core dependencies:

```bash
pip install -r requirements.txt
```

The two **provenance** notebooks (`prepare_covariates.ipynb`, `select_target_areas.ipynb`) additionally
require geospatial packages (`geopandas`, `rasterstats`, `shapely`, `pycountry`, `requests`, `tqdm`,
`googletrans`) and the external datasets above; they are included for transparency and are not needed
to reproduce the headline results.

## How to reproduce

Open each notebook **from the `src/` directory** (paths are written relative to `src/`) and run all
cells, in this order:

1. *(optional — skip this)* `prepare_covariates.ipynb` rebuilds `midsave/control_vars.parquet` from the
   external datasets. The output is already provided, so you can go straight to step 2.
2. `did_analysis.ipynb` — descriptive statistics, the communal and individual (Dhuhr) Instagram DIDs,
   the ambient-religiosity analysis, the A/B test, falsification tests, all figures, and the LaTeX
   tables. Writes `midsave/df_well_glm.parquet` (the analytic dataset used by steps 3–4).
3. `specification_curve.ipynb` — the 96-cell specification curve (Figures 7–8).
4. `wild_bootstrap.ipynb` — wild cluster bootstrap-t inference (`output/wild_bootstrap_summary.csv`),
   the basis for the reported effect bounds.

From the command line you can run, e.g.:

```bash
cd src
jupyter nbconvert --to notebook --execute --inplace did_analysis.ipynb
```

All outputs are written to `output/`.

## World Values Survey analysis

The WVS arm (the sacred-day × prayer-propensity interaction and its robustness checks) is implemented in
**Stata**. The code and instructions are in `wvs/` (see `wvs/README.md`).

## Contact

Till Koebe (corresponding author), Saarland University — till.koebe@uni-saarland.de
