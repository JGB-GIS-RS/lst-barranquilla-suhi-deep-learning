# lst-barranquilla-suhi-deep-learning

Computational notebooks supporting annual land surface temperature (LST) modeling in the Barranquilla Metropolitan Area, Colombia, using Landsat 8/9 Collection 2 Level-2 products, train-only normalization, regional climate-radiative descriptors derived from NASA POWER, and U-Net-based spatiotemporal deep learning models.

## Overview

This repository provides the public computational record for the retrospective component of a remote-sensing study on annual LST and surface urban heat island (SUHI) patterns in the Barranquilla Metropolitan Area.

The workflow documents:

1. Landsat 8/9 scene inventory and annual product generation;
2. quality control and land-domain masking;
3. train-only normalization of LST and spectral indices;
4. derivation of annual regional climate-radiative descriptors from NASA POWER;
5. T3 tensor and patch construction;
6. training of five U-Net-based model configurations (M1–M5);
7. model comparison in normalized and physical temperature units; and
8. export of evaluation tables, diagnostic plots, and manuscript-oriented figures.

This public release is intentionally limited to code, lightweight tabular products, diagnostic figures, and notebook outputs. It does not store the large raster, tensor, patch, checkpoint, or full-resolution prediction products required for a fully self-contained rerun.

## Study area

The study domain covers the Barranquilla Metropolitan Area and its surrounding urban, peri-urban, coastal, and fluvial environments in northern Colombia.

The Landsat WRS-2 reference is:

```text
Path: 009
Row: 052
```

The operational projected coordinate reference system is:

```text
EPSG:32618 — WGS 84 / UTM zone 18N
```

## Temporal scope

The operational Landsat 8/9 period is:

```text
2013–2025
```

The year 2026 is excluded because it does not represent a complete annual observation period comparable with the preceding years.

## T3 temporal formulation

The retrospective modeling task uses three antecedent annual spectral states to estimate the LST of a target year:

```text
Input states: Y−3, Y−2, Y−1
Target:       LST(Y)
```

The fixed target-year partition is:

```text
Training:   2016–2019
Validation: 2020–2022
Test:       2023–2025
```

Normalization parameters are estimated only from the corresponding training data and are then held fixed for validation and test processing. For the T3 spectral inputs, the training predictor period spans 2013–2018; for the LST target and target-year climate descriptors, the training years are 2016–2019.

## Predictor variables

The six Landsat-derived spectral predictors are:

```text
NDVI, NDMI, NDBI, UI, SAVI, BSI
```

The two regional climate-radiative descriptors used by M5 are:

```text
delta_t2m_mean_Y_minus_Yminus1
delta_solar_radiation_mean_Y_minus_Yminus1
```

These NASA POWER variables represent annual regional conditions associated with the model-domain centroid. They are replicated spatially for tensor compatibility and must not be interpreted as climate fields distributed at Landsat pixel resolution.

## Model configurations

The repository includes five model-training notebooks:

| Model | Architecture | Input formulation |
|---|---|---|
| M1 | U-Net baseline | 18-channel spectral T3 stack |
| M2 | SE U-Net | 18-channel spectral T3 stack |
| M3 | ConvLSTM U-Net | Three temporal states × six spectral channels |
| M4 | ConvLSTM-SE U-Net | Three temporal states × six spectral channels |
| M5 | T3-Climate ConvLSTM-SE U-Net | Spectral T3 sequence plus two regional climate-radiative descriptors |

M1–M4 use equivalent spectral information and fixed temporal partitions, enabling architectural comparison under a common predictor structure. M5 is an augmented experimental configuration that adds target-year climate-radiative descriptors to the ConvLSTM-SE framework. Therefore, the M4–M5 contrast is interpreted as a comparison between complete configurations, not as a pure architectural ablation or an isolated causal estimate of climate effects.

## Repository structure

```text
lst-barranquilla-suhi-deep-learning/
|
├── configs/                    Configuration and experiment-metadata files.
│   ├── README.md
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
│       ├── climate_forcings/
│       └── patches/
|
├── notebooks/                  Public notebook sequence for the historical workflow.
│   ├── README.md
│   ├── notebook_index.md
│   ├── 01_scene_inventory_landsat_8_9.ipynb
│   ├── 02_preprocessing_lst_indices.ipynb
│   ├── 03_quality_control.ipynb
│   ├── 04_train_only_normalization.ipynb
│   ├── 05_climate_forcing_integration.ipynb
│   ├── 06_tensor_construction.ipynb
│   ├── 07_patch_extraction.ipynb
│   ├── 08A_train_M1_unet_baseline.ipynb
│   ├── 08B_train_M2_se_unet.ipynb
│   ├── 08C_train_M3_convlstm_unet_v5_2016_2025_dirfix.ipynb
│   ├── 08D_train_M4_convlstm_se_unet.ipynb
│   ├── 08E_train_M5_t3_climate_convlstm_se_unet.ipynb
│   ├── 08F_compare_models_M1_M5.ipynb
│   └── 09_physical_unit_evaluation_and_exports_figures.ipynb
|
├── src/                        Minimal package scaffolding; implemented workflows are contained in the notebooks.
├── .gitignore
├── CITATION.cff
├── LICENSE
├── README.md
├── environment.yml
└── requirements.txt
```

## Notebook sequence

| Notebook | Main purpose |
|---|---|
| `01_scene_inventory_landsat_8_9.ipynb` | Landsat 8/9 scene inventory for 2013–2025 |
| `02_preprocessing_lst_indices.ipynb` | Annual LST and spectral-index preprocessing |
| `03_quality_control.ipynb` | Quality-control masks and valid-domain diagnostics |
| `04_train_only_normalization.ipynb` | Train-only normalization of LST and spectral predictors |
| `05_climate_forcing_integration.ipynb` | NASA POWER annual descriptors and interannual deltas |
| `06_tensor_construction.ipynb` | T3 spectral-climate tensor construction |
| `07_patch_extraction.ipynb` | Spatial patch extraction and temporal split assignment |
| `08A_train_M1_unet_baseline.ipynb` | M1 U-Net baseline training |
| `08B_train_M2_se_unet.ipynb` | M2 SE U-Net training |
| `08C_train_M3_convlstm_unet_v5_2016_2025_dirfix.ipynb` | M3 ConvLSTM U-Net training |
| `08D_train_M4_convlstm_se_unet.ipynb` | M4 ConvLSTM-SE U-Net training |
| `08E_train_M5_t3_climate_convlstm_se_unet.ipynb` | M5 T3-Climate ConvLSTM-SE U-Net training |
| `08F_compare_models_M1_M5.ipynb` | Comparative evaluation of M1–M5 |
| `09_physical_unit_evaluation_and_exports_figures.ipynb` | Conversion to °C, physical-unit evaluation, and figure exports |

This notebook collection represents the final public scope of the repository. No additional notebooks are planned for this public release.

## Included lightweight products

The repository includes lightweight products that support methodological inspection and traceability, such as:

- Landsat scene inventories and annual/monthly scene-count summaries;
- quality-control summaries and file-existence audits;
- train-only normalization parameters and audit tables;
- NASA POWER daily and annual tables, interannual deltas, and metadata;
- diagnostic PNG figures for quality control, normalization, climate descriptors, and patch distribution; and
- stored notebook outputs from the reference execution, including training diagnostics, model comparisons, and physical-unit evaluation figures where available.

## Data not stored in this repository

The following products are intentionally excluded because of size, computational cost, or environment-specific storage requirements:

- raw Landsat scenes;
- full-resolution annual GeoTIFF products;
- aligned raster stacks;
- tensor datasets;
- patch datasets;
- trained model weights and checkpoints;
- full-resolution prediction rasters;
- temporary preprocessing and cache files.

These products remain external to GitHub. Consequently, cloning the repository is sufficient for code and methodological inspection, but not for a fully self-contained end-to-end rerun.

## Execution environment and paths

The notebooks were developed primarily in Google Colab with data stored in Google Drive. Several notebooks therefore contain environment-specific paths that must be adapted before execution.

The files below document the intended environment and path structure:

```text
requirements.txt
environment.yml
configs/paths_example.yml
```

Executing the complete workflow requires access to the external intermediate products, sufficient storage, and GPU resources for model training.

## Reproducibility scope

The repository prioritizes:

- transparent scene inventory and preprocessing logic;
- explicit temporal partitioning;
- train-only normalization;
- documented construction of spectral and climate-radiative inputs;
- inspectable implementations of M1–M5;
- consistent retrospective model comparison; and
- traceable conversion of normalized errors to physical temperature units.

The repository should therefore be interpreted as a substantial methodological and computational record with partial reproducibility, rather than as a fully self-contained data package.

## Citation

Citation metadata are provided in:

```text
CITATION.cff
```

## License

License information is provided in:

```text
LICENSE
```
