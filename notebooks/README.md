# Notebooks

This directory contains the public notebook sequence for the retrospective Barranquilla LST modeling and derived SUHI diagnostic workflow.

The notebooks support methodological inspection, code transparency, and partial reproducibility. Large rasters, tensors, patch archives, checkpoints, and full-resolution prediction products remain external to GitHub because of their size and storage requirements.

## Public workflow

| Order | Notebook | Main purpose |
|---:|---|---|
| 01 | `01_scene_inventory_landsat_8_9.ipynb` | Landsat 8/9 Collection 2 Level-2 scene inventory for 2013–2025. |
| 02 | `02_preprocessing_lst_indices.ipynb` | Quality masking, annual LST, six spectral indices, and annual median composites. |
| 03 | `03_quality_control.ipynb` | Raster geometry, masks, valid-domain coverage, and data-quality audits. |
| 04 | `04_train_only_normalization.ipynb` | Reference-experiment Landsat normalization using fixed 2013–2019 parameters. |
| 05 | `05_climate_forcing_integration.ipynb` | NASA POWER regional climate-radiative descriptors and retained annual differences. |
| 06 | `06_tensor_construction.ipynb` | T3 tensor construction. |
| 07 | `07_patch_extraction.ipynb` | Patch extraction and fixed temporal split assignment. |
| 08A | `08A_train_M1_unet_baseline.ipynb` | M1 U-Net training. |
| 08B | `08B_train_M2_se_unet.ipynb` | M2 SE U-Net training. |
| 08C | `08C_train_M3_convlstm_unet_v5_2016_2025_dirfix.ipynb` | M3 ConvLSTM U-Net training. |
| 08D | `08D_train_M4_convlstm_se_unet.ipynb` | M4 ConvLSTM-SE U-Net training. |
| 08E | `08E_train_M5_t3_climate_convlstm_se_unet.ipynb` | M5 T3-Climate ConvLSTM-SE U-Net training. |
| 08F | `08F_compare_models_M1_M5.ipynb` | M1–M5 retrospective comparison. |
| 09 | `09_physical_unit_evaluation_and_exports_figures.ipynb` | Physical-unit LST evaluation and spatial diagnostics. |
| 10 | `10_emc_built_2022_reference_and_suhi_diagnostics.ipynb` | EMC-BUILT harmonization, non-urban reference definition, SUHI derivation, sensitivity, and error decomposition. |
| 11 | `11_urban_to_peripheral_thermal_gradient_2023_2025.ipynb` | Observed/predicted urban-to-peripheral thermal-gradient reconstruction. |

## Normalization note

Notebook 04 retains its historical `train_only` filename. For the reported experiment, however, Landsat LST and the six spectral indices use normalization parameters estimated from the fixed **2013–2019 pre-validation reference period** and then frozen for later years.

```text
LST: global z-score
mu = 38.48322677612305 °C
sigma = 4.032179355621338 °C

Spectral indices: robust min-max
P2-P98, clipped to [0,1]
```

VALIDATION (2020–2022) and TEST (2023–2025) do not contribute to these Landsat normalization parameters. The two NASA POWER descriptors used by M5 are normalized separately from target TRAIN years 2016–2019.

## Scientific scope

```text
Landsat / NASA POWER
        ↓
annual products + fixed Landsat normalization
        ↓
T3 construction and patch extraction
        ↓
M1–M5 training and independent TEST evaluation
        ↓
full-domain LST spatial diagnostics
        ↓
EMC-BUILT-based non-urban reference
        ↓
continuous SUHI diagnostics
        ↓
urban-to-peripheral thermal gradient
```

LST is the only variable directly modeled by the deep-learning framework. SUHI is derived afterward from observed and predicted LST using an independently defined non-urban reference.

## Temporal protocol

```text
TRAIN      = 2016–2019
VALIDATION = 2020–2022
TEST       = 2023–2025
```

The observed target-year LST is not used as an input predictor. TEST is reserved for independent evaluation and does not control checkpoint selection, early stopping, or learning-rate scheduling.

## Storage boundary

The repository intentionally excludes original Landsat scenes, full-resolution annual GeoTIFFs, raster stacks/cubes, tensor and patch archives, model checkpoints, full-resolution LST/SUHI rasters, and temporary/intermediate Google Drive products.

Accordingly, the repository is a transparent computational record with partial reproducibility rather than a fully self-contained one-click package.

The notebooks are the authoritative executable implementations.
