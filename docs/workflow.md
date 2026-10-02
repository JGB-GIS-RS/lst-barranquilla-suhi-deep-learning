# Methodological workflow

This document summarizes the implemented computational workflow for retrospective annual land surface temperature (LST) modeling and derived surface urban heat island (SUHI) diagnostics in the Barranquilla Metropolitan Area, Colombia.

## 1. Scope

The workflow covers:

- Landsat 8/9 scene inventory and annual surface-product generation;
- quality screening and terrestrial-domain definition;
- fixed pre-validation Landsat normalization;
- NASA POWER regional climate-radiative descriptors;
- T3 tensor and patch construction;
- training and comparison of models M1-M5;
- independent temporal TEST evaluation;
- full-domain and local LST diagnostics;
- EMC-BUILT-based non-urban reference definition;
- continuous SUHI derivation;
- urban SUHI intensity statistics;
- urban-to-peripheral thermal-gradient analysis.

Prospective scenario-based projection is outside the scope of this repository.

## 2. Landsat data and annual products

```text
Collections:
LANDSAT/LC08/C02/T1_L2
LANDSAT/LC09/C02/T1_L2

Operational period: 2013-2025
WRS-2 path/row: 009/052
CRS: EPSG:32618
Nominal resolution: 30 m
```

Quality screening uses `QA_PIXEL`, `QA_RADSAT`, radiometric validity checks, NoData handling, and terrestrial-domain masking. Annual median products are generated for:

```text
LST, NDVI, NDMI, NDBI, UI, SAVI, BSI
```

Valid-observation count layers are retained as diagnostics.

Implementation:

```text
01_scene_inventory_landsat_8_9.ipynb
02_preprocessing_lst_indices.ipynb
03_quality_control.ipynb
```

## 3. Landsat normalization

For the reported experiment, annual Landsat LST and spectral-index normalization parameters are estimated from the fixed pre-validation period:

```text
2013-2019
```

and frozen for subsequent years. VALIDATION and TEST do not contribute to their estimation.

LST uses a global z-score:

```text
mu_ref    = 38.48322677612305 °C
sigma_ref = 4.032179355621338 °C
```

Spectral predictors use robust min-max scaling based on:

```text
P2 and P98
```

with clipping to `[0,1]`.

The historical notebook filename retains `train_only` for traceability; the label indicates exclusion of VALIDATION/TEST from parameter estimation and does not mean that Landsat parameters were based only on target TRAIN years 2016-2019.

Implementation:

```text
04_train_only_normalization.ipynb
```

## 4. NASA POWER descriptors

The final M5 configuration uses:

```text
delta_t2m_mean_Y_minus_Yminus1
delta_solar_radiation_mean_Y_minus_Yminus1
```

These are annual regional descriptors associated with the model-domain centroid. They are spatially replicated only for tensor compatibility and are not interpreted as 30 m intra-urban climate fields.

Their normalization is separate from Landsat normalization and uses the target TRAIN years 2016-2019.

Implementation:

```text
05_climate_forcing_integration.ipynb
```

## 5. T3 formulation and temporal partition

```text
Y-3, Y-2, Y-1 -> LST(Y)
```

```text
TRAIN:      2016-2019
VALIDATION: 2020-2022
TEST:       2023-2025
```

The observed LST of target year Y is not an input predictor.

Patch design:

```text
Patch size: 128 x 128 pixels
Training stride: 64
Validation stride: 128
TEST stride: 128
Minimum valid-pixel fraction: 0.70
```

Implementation:

```text
06_tensor_construction.ipynb
07_patch_extraction.ipynb
```

## 6. Model family

| Model | Architecture | Input |
|---|---|---|
| M1 | U-Net | 18-channel spectral T3 stack |
| M2 | SE U-Net | 18-channel spectral T3 stack |
| M3 | ConvLSTM U-Net | 3 x 6 spectral sequence |
| M4 | ConvLSTM-SE U-Net | 3 x 6 spectral sequence |
| M5 | T3-Climate ConvLSTM-SE U-Net | T3 spectral sequence + two regional descriptors |

M1-M4 form the controlled spectral-model comparison. M5 is an augmented complete configuration, so M4-M5 is not interpreted as a pure causal ablation.

Implementation:

```text
08A_train_M1_unet_baseline.ipynb
08B_train_M2_se_unet.ipynb
08C_train_M3_convlstm_unet_v5_2016_2025_dirfix.ipynb
08D_train_M4_convlstm_se_unet.ipynb
08E_train_M5_t3_climate_convlstm_se_unet.ipynb
```

## 7. Training and leakage control

Validation controls checkpoint selection, learning-rate scheduling, and early stopping. TEST does not control model optimization.

The workflow prevents the principal forms of temporal leakage:

- target-year LST is not used as predictor;
- antecedent LST is not used as predictor;
- Landsat normalization uses only the 2013-2019 pre-validation reference period;
- NASA POWER descriptor normalization uses target TRAIN years 2016-2019;
- temporal splits are fixed before model optimization;
- validation controls checkpoint selection;
- TEST is reserved for final evaluation.

## 8. Model evaluation

Evaluation reports RMSE, MAE, bias, R2, and valid-pixel count by target year and for the aggregated TEST period.

Implementation:

```text
08F_compare_models_M1_M5.ipynb
09_physical_unit_evaluation_and_exports_figures.ipynb
```

Patch-based TEST metrics and reconstructed full-domain metrics are kept distinct because they differ in spatial support and reconstruction procedures.

## 9. Full-domain spatial diagnostics

The reconstructed full-domain rasters are used for observed/predicted LST comparison, residual mapping, pixel-wise agreement, local spatial diagnostics, and derived SUHI analysis. These spatial products complement but do not replace patch-based TEST metrics.

## 10. EMC-BUILT harmonization

Derived SUHI diagnostics use EMC-BUILT R2025A, reference epoch 2022. The source product is harmonized to the canonical 30 m grid with extensive-area aggregation.

```text
BU_all = built-up fraction >= 0.10
BU_core = 8-neighbor connected components >= 0.333 km²
```

Implementation:

```text
10_emc_built_2022_reference_and_suhi_diagnostics.ipynb
```

## 11. Non-urban reference R

The final reference is:

```text
R = 4-6 km from BU_core
    intersect fixed TEST support
    excluding BU_all
```

Final geometry:

```text
167,527 pixels
150.7743 km²
```

Sensitivity is assessed for `A_min = 0.25, 0.333, 0.50 km²`.

## 12. Derived SUHI

For each TEST year:

```text
SUHI_obs  = LST_obs  - Tref_obs
SUHI_pred = LST_pred - Tref_pred
Residual  = SUHI_pred - SUHI_obs
```

The annual reference temperature is the median LST over valid pixels of R. The urban support is:

```text
U = BU_core intersect annual common SUHI support
```

Principal diagnostics include urban median SUHI, urban P95, observed/predicted continuous SUHI fields, residuals, sensitivity, and urban/reference error decomposition.

## 13. Urban-to-peripheral thermal gradient

The final gradient uses:

```text
Urban core -> 0-2 km -> 2-4 km -> 4-6 km -> 6-8 km
```

Observed and predicted gradients are reconstructed independently for 2023, 2024, and 2025.

Implementation:

```text
11_urban_to_peripheral_thermal_gradient_2023_2025.ipynb
```

## 14. Repository boundary

The repository includes code, documentation, configuration references, lightweight tables, diagnostic figures, and notebook logic. It intentionally excludes large-volume source scenes, full-resolution annual rasters, tensor/patch archives, checkpoints, and full-resolution LST/SUHI rasters.

## 15. Final workflow

```text
Data
-> annual products
-> fixed pre-validation Landsat normalization
-> T3 formulation
-> M1-M5
-> independent TEST evaluation
-> full-domain LST diagnostics
-> EMC-BUILT non-urban reference
-> Tref
-> continuous SUHI
-> urban intensity + gradient diagnostics
```

The public notebook sequence is complete through Notebook 11 for the declared retrospective manuscript scope.
