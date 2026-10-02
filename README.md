# Retrospective LST Modeling and Derived SUHI Diagnostics for Barranquilla

Computational workflow for retrospective annual land surface temperature (LST) modeling and derived surface urban heat island (SUHI) diagnostics in the Barranquilla Metropolitan Area, Colombia.

The repository integrates Landsat 8/9 Collection 2 Level-2 products, fixed pre-validation Landsat normalization, regional climate-radiative descriptors from NASA POWER, and U-Net-based spatiotemporal deep-learning architectures under a temporally controlled training, validation, and independent TEST design.

## 1. Scope

The workflow has two connected components:

1. **Retrospective LST estimation** using annual Landsat-derived spectral trajectories, a T3 temporal formulation, five deep-learning configurations, independent temporal evaluation for 2023–2025, and full-domain/local spatial diagnostics.
2. **Derived SUHI diagnostics** using an independent EMC-BUILT-based non-urban reference, annual reference temperatures, continuous observed/predicted SUHI, urban intensity statistics, and urban-to-peripheral thermal gradients.

LST is the only directly modeled variable. SUHI is derived afterward from observed and predicted LST.

Prospective scenario-based projections are outside the scope of this repository.

## 2. Study area

```text
Landsat WRS-2 path/row: 009/052
Projected CRS: EPSG:32618 — WGS 84 / UTM zone 18N
Nominal spatial resolution: 30 m
```

## 3. Temporal scope

```text
Operational Landsat period: 2013–2025
T3 formulation: Y-3, Y-2, Y-1 -> LST(Y)

TRAIN:      2016–2019
VALIDATION: 2020–2022
TEST:       2023–2025
```

The TEST period is kept independent from model training, learning-rate control, early stopping, checkpoint selection, and hyperparameter adjustment.

## 4. Input data

### Landsat

```text
LANDSAT/LC08/C02/T1_L2
LANDSAT/LC09/C02/T1_L2
```

The six Landsat-derived spectral predictors are:

```text
NDVI
NDMI
NDBI
UI
SAVI
BSI
```

Annual LST and spectral products are summarized using a pixel-wise median after quality screening.

### NASA POWER

M5 uses two annual regional descriptors:

```text
delta_t2m_mean_Y_minus_Yminus1
delta_solar_radiation_mean_Y_minus_Yminus1
```

They are replicated spatially for tensor compatibility and are not interpreted as 30 m intra-urban climate fields.

### EMC-BUILT

The derived SUHI workflow uses EMC-BUILT R2025A, reference epoch 2022, as an independent built-up surface product.

## 5. Spatial harmonization and normalization

All annual products are aligned to a common canonical grid:

```text
CRS: EPSG:32618
Resolution: 30 m
Raster size: 1115 × 1257 pixels
```

For the reported reference experiment, Landsat normalization parameters are estimated from the fixed **2013–2019 pre-validation reference period** and then frozen for later years:

```text
LST: global z-score
mu = 38.48322677612305 °C
sigma = 4.032179355621338 °C

Spectral indices: robust min-max
P2–P98, clipped to [0,1]
```

VALIDATION (2020–2022) and TEST (2023–2025) do not contribute to estimation of these Landsat parameters.

The historical notebook/path label `train_only` is retained for traceability. For Landsat variables, it is a leakage-control label rather than a literal statement that parameters were estimated only from target TRAIN years 2016–2019.

The two NASA POWER descriptors used by M5 are normalized separately using target TRAIN years **2016–2019**.

## 6. T3 formulation

For each target year, the model uses three antecedent spectral states:

```text
Y-3
Y-2
Y-1
```

The target-year observed LST is used only as the supervised response.

Patch configuration:

```text
Patch size: 128 × 128 pixels
Training stride: 64 pixels
Validation stride: 128 pixels
TEST stride: 128 pixels
Minimum valid-pixel fraction: 0.70
```

## 7. Deep-learning configurations

| Model | Architecture | Input formulation |
|---|---|---|
| M1 | U-Net | 18-channel spectral T3 stack |
| M2 | SE U-Net | 18-channel spectral T3 stack |
| M3 | ConvLSTM U-Net | Three temporal states × six spectral indices |
| M4 | ConvLSTM-SE U-Net | Three temporal states × six spectral indices |
| M5 | T3-Climate ConvLSTM-SE U-Net | Spectral T3 sequence + two regional climate-radiative descriptors |

M1–M4 share the same spectral predictor content. M5 is a complete augmented configuration and uses a model-specific training protocol; therefore M4–M5 is not interpreted as a pure causal ablation.

## 8. Model evaluation

Evaluation metrics include RMSE, MAE, bias, R², and valid-pixel count.

For M5, aggregated independent TEST performance is:

```text
RMSE = 1.813 °C
MAE  = 1.399 °C
Bias = -0.297 °C
R²   = 0.789
```

Patch-based TEST metrics and reconstructed full-domain metrics are treated as distinct evaluation products.

Notebook 09 uses the same reference LST normalization parameters shown above for inverse conversion to °C.

## 9. Non-urban reference and SUHI

EMC-BUILT is harmonized to the canonical grid. The principal masks are:

```text
BU_all:  built-up fraction >= 0.10
BU_core: connected components >= 0.333 km²
R:       4–6 km from BU_core, within fixed TEST support, excluding BU_all
```

Final reference geometry:

```text
Pixels: 167,527
Area:   150.7743 km²
```

For each TEST year:

```text
SUHI_obs  = LST_obs  - Tref_obs
SUHI_pred = LST_pred - Tref_pred
```

The urban support for summary statistics is:

```text
U = BU_core intersect annual common SUHI support
```

SUHI is retained as a continuous variable in degrees Celsius.

## 10. Urban-to-peripheral thermal gradient

The final gradient uses:

```text
Urban core
0–2 km
2–4 km
4–6 km
6–8 km
```

The 4–6 km band serves as the reference level. The 8–10 km band is retained only as a diagnostic because of truncated support.

## 11. Repository structure

```text
configs/
data/
docs/
notebooks/
README.md
requirements.txt
environment.yml
LICENSE
CITATION.cff
```

Notebook sequence:

```text
01_scene_inventory_landsat_8_9.ipynb
02_preprocessing_lst_indices.ipynb
03_quality_control.ipynb
04_train_only_normalization.ipynb
05_climate_forcing_integration.ipynb
06_tensor_construction.ipynb
07_patch_extraction.ipynb
08A_train_M1_unet_baseline.ipynb
08B_train_M2_se_unet.ipynb
08C_train_M3_convlstm_unet_v5_2016_2025_dirfix.ipynb
08D_train_M4_convlstm_se_unet.ipynb
08E_train_M5_t3_climate_convlstm_se_unet.ipynb
08F_compare_models_M1_M5.ipynb
09_physical_unit_evaluation_and_exports_figures.ipynb
10_emc_built_2022_reference_and_suhi_diagnostics.ipynb
11_urban_to_peripheral_thermal_gradient_2023_2025.ipynb
```

## 12. Reproducibility scope

The repository provides methodological inspection, computational traceability, lightweight parameter/audit products, model definitions, evaluation logic, and derived SUHI workflows.

Large products are intentionally excluded, including original Landsat scenes, full-resolution annual rasters, aligned stacks/cubes, tensor and patch archives, trained checkpoints, and full-resolution LST/SUHI products. The repository is therefore not a fully self-contained one-click reproduction package.

The notebooks are the authoritative executable implementations.
