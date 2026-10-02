# Data

This directory contains lightweight data products that support methodological inspection, traceability, and partial reproducibility of the Barranquilla LST/SUHI workflow.

The repository intentionally excludes large geospatial and machine-learning artifacts. Only compact tabular, metadata, audit, and diagnostic products are included.

## Study data scope

```text
Operational period: 2013-2025
Landsat WRS-2 path/row: 009/052
Projected coordinate reference system: EPSG:32618
```

The year 2026 is excluded because it does not represent a complete annual period comparable with the 2013-2025 products.

Spectral variables:

```text
NDVI, NDMI, NDBI, UI, SAVI, BSI
```

## Directory structure

```text
data/
├── README.md
├── scene_inventory/
├── quality_control/
├── normalization/
├── climate_forcings/
└── suhi_diagnostics/
```

## `scene_inventory/`

Contains lightweight tables derived from the Landsat 8/9 scene inventory, including temporal coverage and acquisition summaries.

## `quality_control/`

Contains compact reports documenting file availability, raster geometry and grid consistency, valid-pixel counts, land-domain/common-valid-mask summaries, annual LST/index audits, and detection of missing or inconsistent products.

## `normalization/`

Contains parameters and audit products associated with the normalization stage.

For the reported reference experiment:

- annual Landsat LST and spectral-index normalization parameters are estimated from the fixed pre-validation period **2013-2019**;
- LST uses a global z-score with `mu = 38.48322677612305 °C` and `sigma = 4.032179355621338 °C`;
- the six spectral indices use robust min-max normalization based on **P2-P98**, clipped to `[0,1]`;
- these Landsat parameters are frozen for later years, so VALIDATION (2020-2022) and TEST (2023-2025) do not influence their estimation;
- the two NASA POWER descriptors used by M5 are normalized separately from target TRAIN years **2016-2019**.

Historical filenames retain the `train_only` label for traceability. For Landsat variables, this label indicates exclusion of validation/test statistics rather than literal use of only the supervised target TRAIN years.

The authoritative compact parameter records are:

```text
data/normalization/04_normalization_parameters_train_only_2013_2025.csv
data/normalization/04_normalization_parameters_train_only_2013_2025.json
```

## `climate_forcings/`

Contains lightweight annual NASA POWER tables and related diagnostics used to construct the regional climate-radiative descriptors associated with the target year.

The final M5 configuration uses:

```text
delta_t2m_mean_Y_minus_Yminus1
delta_solar_radiation_mean_Y_minus_Yminus1
```

These variables are treated as annual regional descriptors for the model-domain centroid, not as pixel-level 30 m climate fields.

## `suhi_diagnostics/`

Contains the lightweight tabular outputs exported by Notebooks 10 and 11 for the final non-urban reference, SUHI, sensitivity, urban-support, spectral-control, and urban-to-peripheral gradient analyses.

These tables make the principal numerical SUHI/reference claims directly inspectable without requiring the complete external GeoTIFF archive. The folder includes `reference_stats.csv`, `reference_support.csv`, `sensitivity_R.csv`, `sensitivity_suhi_Amin_025_0333_050.csv`, `urban_support_comparison_BUall_vs_BUcore.csv`, `suhi_p95_domain_vs_urban.csv`, `suhi_error_decomposition_2023_2025.csv`, `suhi_summary_final.csv`, `thermal_gradient.csv`, `thermal_stats.csv`, and `spectral_stats.csv`.

See `data/suhi_diagnostics/README.md` for the file-level manifest and frozen reference definition.

## Data not stored in this repository

The repository excludes original Landsat scenes, full-resolution annual GeoTIFFs, aligned raster stacks/cubes, tensor datasets, patch datasets, trained model weights/checkpoints, full-resolution prediction/residual maps, and temporary intermediate files.

## Reproducibility scope

Complete regeneration requires external access to the original satellite data, large intermediate products, adequate storage, and suitable computational resources.

Related documentation:

```text
docs/data_sources.md
docs/preprocessing.md
docs/workflow.md
notebooks/README.md
notebooks/notebook_index.md
```
