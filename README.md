# lst-barranquilla-suhi-deep-learning

Computational workflow for modeling land surface temperature (LST) and surface urban heat island (SUHI) patterns in Barranquilla, Colombia, using Landsat 8/9 Collection 2 Level-2 products, train-only normalization, interannual NASA POWER climate forcings, and spatial-temporal deep learning models.

## Overview

This repository supports the reproducibility framework of a remote-sensing study focused on spatial-temporal modeling of LST in the urban and peri-urban domain of Barranquilla, Colombia.

The workflow uses Landsat 8/9 Collection 2 Level-2 products to derive annual LST and spectral indices. These variables are quality-controlled, normalized using training-only statistics, and organized into multi-temporal inputs for deep learning models. The final model configuration incorporates selected interannual climate descriptors derived from NASA POWER as annual regional context.

The repository is intended to improve transparency, reproducibility, and methodological traceability. It does not store large satellite images, full-resolution raster stacks, tensor datasets, patch datasets, trained model checkpoints, or full-resolution prediction maps.

## Study area

The study area corresponds to Barranquilla, Colombia, and its surrounding urban and peri-urban environment.

The Landsat WRS-2 reference used for the scene inventory is:

```text
Path: 9
Row: 52
```

The operational projected coordinate reference system is:

```text
EPSG:32618
```

## Temporal scope

The operational period of the Landsat 8/9 workflow is:

```text
2013-2025
```

The year 2026 is excluded from the operational annual workflow because the annual period is incomplete.

## Temporal formulation

The modeling task is formulated as spatial-temporal regression. The model uses three antecedent annual states to predict the LST of a target year:

```text
T1 = Y - 3
T2 = Y - 2
T3 = Y - 1
Target = LST(Y)
```

The final target-year partitioning is:

```text
Training target years:   2016-2019
Validation target years: 2020-2022
Test target years:       2023-2025
```

Train-only normalization parameters are estimated from the training period only and then fixed for validation and test years.

## Main methodological components

The workflow is organized around the following components:

1. Landsat 8/9 scene inventory and metadata organization.
2. Annual preprocessing of Landsat-derived LST and spectral indices.
3. Quality control of valid pixels, masks, and annual products.
4. Train-only normalization of spectral indices and LST.
5. NASA POWER annual climate forcing table construction and interannual delta derivation.
6. Multi-temporal tensor construction.
7. Spatial-temporal patch extraction.
8. Baseline and deep learning model training.
9. Model comparison using statistical and spatial metrics.
10. Prediction maps and spatial diagnostics.

## Final explanatory variables

The Landsat-derived spectral predictors are:

```text
NDVI, NDMI, NDBI, UI, SAVI, BSI
```

The final climate predictors retained for the climate-enhanced model are:

```text
delta_t2m_mean_Y_minus_Yminus1
delta_solar_radiation_mean_Y_minus_Yminus1
```

These NASA POWER variables are annual regional descriptors derived for the model-domain centroid. They must not be interpreted as pixel-level climate rasters.

## Model comparison logic

The repository implements a progressive model comparison design.

Models 1–4 use the same spectral T3 input data, derived from three antecedent years (Y−3, Y−2, Y−1) and six Landsat-based spectral indices per year. These models differ only in architecture, allowing the effect of spatial encoding, temporal recurrence, and channel attention to be evaluated under identical data conditions.

The final model, referred to as the T3-Climate ConvLSTM-SE U-Net, uses the same spectral T3 structure and adds two annual interannual climate predictors derived from NASA POWER: the change in 2-m air temperature and the change in surface solar radiation between Y and Y−1. This final comparison isolates the added value of climate augmentation after the best spectral-temporal architecture has been established.

## Repository structure

```text
lst-barranquilla-suhi-deep-learning/
|
├── configs/                    Configuration files for paths, models, training, and experiment metadata.
│   ├── experiment_metadata.yml
│   ├── model_config.yml
│   ├── paths_example.yml
│   └── training_config.yml
|
├── data/                       Lightweight tabular products and data documentation.
│   ├── README.md
│   ├── scene_inventory/
│   ├── quality_control/
│   ├── normalization/
│   └── climate_forcings/
|
├── docs/                       Methodological and reproducibility documentation.
│   ├── README.md
│   ├── data_sources.md
│   ├── preprocessing.md
│   ├── modeling.md
│   ├── workflow.md
│   ├── reproducibility_notes.md
│   ├── repository_status.md
│   └── figures/
│       ├── quality_control/
│       ├── normalization/
│       └── climate_forcings/
|
├── notebooks/                  Executable notebooks documenting the workflow.
│   ├── README.md
│   ├── notebook_index.md
│   ├── 01_scene_inventory_landsat_8_9.ipynb
│   ├── 02_preprocessing_lst_indices.ipynb
│   ├── 03_quality_control.ipynb
│   └── 04_train_only_normalization.ipynb
|
├── src/                        Source-code modules to be added as the workflow is consolidated.
├── .gitignore
├── CITATION.cff
├── LICENSE
├── README.md
├── environment.yml
└── requirements.txt
```

## Included lightweight outputs

The repository may include lightweight CSV, JSON, and PNG products that support reproducibility, including:

- Landsat scene inventory tables.
- Annual scene-count summaries.
- Quality-control summaries.
- Normalization parameters and audits.
- NASA POWER climate forcing tables and selection traces.
- Diagnostic figures for quality control, normalization, and climate forcing analysis.

## Data not stored in this repository

The following files are intentionally excluded from GitHub:

- Raw Landsat scenes.
- Full-resolution GeoTIFF products.
- Raster stacks.
- Tensor datasets.
- Patch datasets.
- Trained model weights and checkpoints.
- Full-resolution prediction maps.
- Temporary preprocessing outputs.

These products should be reconstructed from the documented workflow or stored externally using appropriate research-data infrastructure.

## Notebook sequence

The notebook sequence is documented in:

```text
notebooks/notebook_index.md
```

Current completed notebooks include:

```text
01_scene_inventory_landsat_8_9.ipynb
02_preprocessing_lst_indices.ipynb
03_quality_control.ipynb
04_train_only_normalization.ipynb
```

The next methodological block is:

```text
05_climate_forcing_integration.ipynb
```

This notebook will document the NASA POWER annual climate forcing workflow for 2013-2025.

## Reproducibility policy

The repository prioritizes:

- transparent scene selection;
- documented preprocessing decisions;
- train-only normalization;
- explicit leakage control;
- reproducible climate-forcing derivation;
- controlled model comparison;
- clear separation between lightweight documentation and large computational outputs.

## Citation

Citation information is provided in:

```text
CITATION.cff
```

## License

License information is provided in:

```text
LICENSE
```
