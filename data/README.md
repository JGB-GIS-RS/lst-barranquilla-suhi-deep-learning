# Data

This directory documents the data sources and lightweight tabular products used in the Barranquilla LST/SUHI workflow.

The repository does not store large geospatial datasets. It may include lightweight CSV, JSON, and metadata products that improve transparency and reproducibility.

## Data scope

The study uses Landsat 8/9 Collection 2 Level-2 products to derive annual land surface temperature and spectral indices for Barranquilla, Colombia.

The operational period is:

```text
2013-2025
```

The year 2026 is excluded from the operational annual workflow because the annual period is incomplete.

The Landsat WRS-2 reference used for the scene inventory is:

```text
Path: 9
Row: 52
```

## Expected directory structure

```text
data/
|
├── README.md
├── scene_inventory/
├── quality_control/
├── normalization/
└── climate_forcings/
```

## Scene inventory

The `scene_inventory/` directory contains lightweight tables derived from the Landsat 8/9 scene inventory.

Expected files include:

- `scene_inventory_landsat89_2013_2025.csv`
- `annual_scene_count_landsat89_2013_2025.csv`
- `monthly_scene_count_landsat89_2013_2025.csv`

These files allow reviewers to inspect temporal availability without downloading large raster datasets.

## Quality control

The `quality_control/` directory contains lightweight reports produced by the quality-control workflow.

These reports document file existence, geometry consistency, valid-pixel availability, land-domain masks, common valid masks, and annual product audits.

## Normalization

The `normalization/` directory contains train-only normalization parameters and audit tables.

Expected products include:

- normalization parameters for LST and spectral indices;
- geometry and file-existence audits;
- normalized-product value summaries;
- train spatial mask summaries;
- JSON summaries supporting reproducibility.

## Climate forcings

The `climate_forcings/` directory is reserved for lightweight annual NASA POWER climate forcing tables and diagnostics.

NASA POWER variables are treated as annual regional descriptors associated with the model-domain centroid. They are not pixel-level spatial rasters.

Expected products include annual climate tables, interannual deltas, selected climate input variables for the final model, and variable-selection traces.

## Data not stored in this repository

Large geospatial and machine-learning products are not stored in this repository, including:

- original Landsat scenes;
- full-resolution GeoTIFF products;
- raster stacks;
- tensor datasets;
- patch datasets;
- trained model weights;
- model checkpoints;
- large prediction maps;
- temporary preprocessing outputs.

These files should be reconstructed from the documented workflow or stored externally.

## Reconstruction workflow

Data reconstruction instructions are documented in:

```text
docs/data_sources.md
docs/preprocessing.md
docs/workflow.md
notebooks/notebook_index.md
```
