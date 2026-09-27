# Methodological workflow

This document summarizes the implemented computational workflow for retrospective
annual land surface temperature (LST) modeling and derived surface urban heat
island (SUHI) diagnostics in the Barranquilla Metropolitan Area, Colombia.

## 1. Scope

The workflow covers:

- Landsat 8/9 scene inventory and annual surface-product generation;
- quality screening and terrestrial-domain definition;
- train-only normalization;
- NASA POWER regional climate-radiative descriptors;
- T3 tensor and patch construction;
- training and comparison of models M1–M5;
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

Operational period: 2013–2025
WRS-2 path/row: 009/052
CRS: EPSG:32618
Nominal resolution: 30 m
```

Quality screening uses `QA_PIXEL`, `QA_RADSAT`, radiometric validity checks,
NoData handling, and terrestrial-domain masking.

Annual median products are generated for:

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

## 3. Train-only normalization

LST uses a training-only global z-score. Spectral predictors use robust
training-only min-max scaling based on the 2.5th and 97.5th percentiles.

Training information only is used to estimate normalization parameters.

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

These are annual regional descriptors associated with the model-domain centroid.
They are spatially replicated only for tensor compatibility and are not
interpreted as 30 m intra-urban climate fields.

Implementation:

```text
05_climate_forcing_integration.ipynb
```

## 5. T3 formulation and temporal partition

```text
Y-3, Y-2, Y-1 → LST(Y)
```

```text
TRAIN:      2016–2019
VALIDATION: 2020–2022
TEST:       2023–2025
```

The observed LST of target year Y is not an input predictor.

Patch design:

```text
Patch size: 128 × 128 pixels
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
| M3 | ConvLSTM U-Net | 3 × 6 spectral sequence |
| M4 | ConvLSTM-SE U-Net | 3 × 6 spectral sequence |
| M5 | T3-Climate ConvLSTM-SE U-Net | T3 spectral sequence + two regional descriptors |

M1–M4 form the controlled spectral-model comparison. M5 is an augmented complete
configuration, so M4–M5 is not interpreted as a pure causal ablation.

Implementation:

```text
08A_train_M1_unet_baseline.ipynb
08B_train_M2_se_unet.ipynb
08C_train_M3_convlstm_unet_v5_2016_2025_dirfix.ipynb
08D_train_M4_convlstm_se_unet.ipynb
08E_train_M5_t3_climate_convlstm_se_unet.ipynb
```

## 7. Training and leakage control

The training protocol uses validation for checkpoint selection, learning-rate
control, and early stopping. TEST does not control model optimization.

The workflow prevents the principal forms of temporal leakage:

- target-year LST is not used as predictor;
- antecedent LST is not used as predictor;
- normalization uses training data only;
- temporal splits are fixed before model optimization;
- validation controls checkpoint selection;
- TEST is reserved for final evaluation.

## 8. Model evaluation

Evaluation reports:

```text
RMSE
MAE
Bias
R²
valid-pixel count
```

Metrics are produced by year and for the aggregated TEST period.

Implementation:

```text
08F_compare_models_M1_M5.ipynb
09_physical_unit_evaluation_and_exports_figures.ipynb
```

Patch-based TEST metrics and reconstructed full-domain metrics are kept distinct
because they differ in spatial support and reconstruction procedures.

## 9. Full-domain spatial diagnostics

The reconstructed full-domain rasters are used for:

- observed/predicted LST comparison;
- residual mapping;
- pixel-wise agreement;
- local spatial diagnostics;
- derived SUHI analysis.

These spatial products complement, but do not replace, patch-based TEST metrics.

## 10. EMC-BUILT harmonization

Derived SUHI diagnostics use EMC-BUILT R2025A, reference epoch 2022.

The source product is harmonized to the canonical 30 m grid with an extensive
area aggregation using `Resampling.sum`.

Built-up fraction:

```text
f_BU = built-up area / 900 m²
```

General built-up mask:

```text
BU_all = f_BU >= 0.10
```

Consolidated built-up core:

```text
8-neighbor connected components
minimum component area = 0.333 km²
```

Nominal result:

```text
BU_core components: 18
```

Implementation:

```text
10_emc_built_2022_reference_and_suhi_diagnostics.ipynb
```

## 11. Non-urban reference R

Distance bands are generated from `BU_core`. Built-up surfaces from `BU_all`
are excluded.

The final reference is:

```text
R = 4–6 km from BU_core
    ∩ fixed TEST support
    ∩ not BU_all
```

Final geometry:

```text
167,527 pixels
150.7743 km²
```

The selection is based on observed thermal stabilization and independent NDVI/NDBI
controls, not on maximizing the final SUHI magnitude.

Sensitivity is assessed for:

```text
A_min = 0.25, 0.333, 0.50 km²
```

## 12. Derived SUHI

For each TEST year:

```text
SUHI_obs  = LST_obs  - Tref_obs
SUHI_pred = LST_pred - Tref_pred
Residual  = SUHI_pred - SUHI_obs
```

The annual reference temperature is the median LST over valid pixels of R.

The urban support for summary statistics is:

```text
U = BU_core ∩ annual common SUHI support
```

Principal diagnostics:

- urban median SUHI;
- urban P95;
- observed and predicted continuous SUHI fields;
- SUHI residual;
- urban intensity distribution;
- sensitivity of the SUHI diagnosis;
- urban/reference error decomposition.

SUHI is retained as a continuous variable in degrees Celsius. Qualitative
intensity classes are not part of the principal analysis.

Implementation:

```text
10_emc_built_2022_reference_and_suhi_diagnostics.ipynb
```

## 13. Urban-to-peripheral thermal gradient

The final gradient uses:

```text
Urban core → 0–2 km → 2–4 km → 4–6 km → 6–8 km
```

The 8–10 km band remains in the audit table but is excluded from the principal
figure because its spatial support is strongly truncated.

For peripheral zone z:

```text
Delta LST_z = median(LST_z) - Tref
```

Observed and predicted gradients are reconstructed independently for 2023, 2024,
and 2025.

Implementation:

```text
11_urban_to_peripheral_thermal_gradient_2023_2025.ipynb
```

## 14. Repository boundary

The repository includes code, documentation, configuration references,
lightweight tables, diagnostic figures, and notebook logic.

It intentionally excludes large-volume products such as source scenes,
full-resolution annual rasters, tensor archives, patch archives, checkpoints,
and full-resolution LST/SUHI rasters.

Complete regeneration therefore requires access to the external research archive
and suitable compute/storage resources.

## 15. Final workflow

```text
Data
→ annual products
→ train-only normalization
→ T3 formulation
→ M1–M5
→ independent TEST evaluation
→ full-domain LST diagnostics
→ EMC-BUILT non-urban reference
→ Tref
→ continuous SUHI
→ urban intensity + gradient diagnostics
```

The public notebook sequence is complete through Notebook 11 for the declared
retrospective manuscript scope.
