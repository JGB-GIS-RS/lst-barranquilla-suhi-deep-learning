# Data

This directory contains lightweight data products that support the methodological
inspection, traceability, and partial reproducibility of the Barranquilla LST/SUHI
workflow.

The repository intentionally excludes large geospatial and machine-learning
artifacts. Only compact tabular, metadata, audit, and diagnostic products are
included.

## Study data scope

The study uses Landsat 8 and Landsat 9 Collection 2 Level-2 products to derive
annual land surface temperature (LST) and six spectral indices for the
Barranquilla Metropolitan Area, Colombia.

```text
Operational period: 2013-2025
Landsat WRS-2 path/row: 009/052
Projected coordinate reference system: EPSG:32618
```

The year 2026 is excluded because it does not represent a complete annual period
comparable with the 2013-2025 products.

The spectral variables used in the modeling workflow are:

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
└── climate_forcings/
```

## `scene_inventory/`

This directory contains lightweight tables derived from the Landsat 8/9 scene
inventory. These products document image availability and temporal coverage
without requiring the original Landsat scenes.

The tables may include:

- the complete 2013-2025 scene inventory;
- annual scene counts;
- monthly scene counts;
- selected acquisition metadata.

## `quality_control/`

This directory contains compact reports generated during the quality-control
workflow. Depending on the processing stage, these reports document:

- file availability;
- raster geometry and grid consistency;
- valid-pixel counts and fractions;
- land-domain and common-valid-mask summaries;
- annual LST and spectral-index audits;
- detection of missing, inconsistent, or anomalous products.

These reports support inspection of the preprocessing chain but do not replace
the full-resolution raster products used in the analysis.

## `normalization/`

This directory contains parameters and audit products associated with train-only
normalization.

The workflow uses:

- global z-score normalization for LST, with parameters estimated only from the
  training target years;
- robust min-max normalization for the spectral indices, using training-only
  percentile limits;
- fixed parameters applied without recalibration to validation and test data.

The directory may include normalization parameters, JSON summaries, value-range
audits, geometry checks, and spatial-mask summaries.

## `climate_forcings/`

This directory contains lightweight annual NASA POWER tables and related
diagnostics used to construct the regional climate-radiative descriptors
associated with the target year.

The public folder name `climate_forcings` is retained for consistency with the
notebooks and repository history. Scientifically, these variables are treated as
annual regional descriptors for the model-domain centroid, not as spatially
distributed climate fields or pixel-level rasters.

The final M5 configuration uses:

```text
delta_t2m_mean_Y_minus_Yminus1
delta_solar_radiation_mean_Y_minus_Yminus1
```

The directory may include:

- annual NASA POWER summaries;
- interannual differences;
- normalized descriptor tables;
- exploratory climate-error diagnostics;
- records supporting the retained M5 inputs.

## Data not stored in this repository

The following products are intentionally excluded because of file size,
computational cost, licensing, or storage considerations:

- original Landsat scenes;
- full-resolution annual GeoTIFF products;
- raster stacks and aligned multi-year cubes;
- tensor datasets;
- patch datasets;
- trained model weights and checkpoints;
- full-resolution prediction and residual maps;
- temporary preprocessing and intermediate files.

These exclusions are deliberate and do not indicate missing repository content.
The public release is designed to document the scientific workflow and provide
the lightweight evidence needed to inspect its principal processing and modeling
decisions.

## Reproducibility scope

The files in this directory support methodological verification and partial
reproducibility. Complete regeneration of the analysis requires external access
to the original satellite data, the large intermediate products, adequate storage,
and suitable computational resources.

The corresponding reconstruction logic and execution sequence are documented in:

```text
docs/data_sources.md
docs/preprocessing.md
docs/workflow.md
notebooks/README.md
notebooks/notebook_index.md
```

The notebooks and manuscript remain the authoritative descriptions of the
implemented workflow.
