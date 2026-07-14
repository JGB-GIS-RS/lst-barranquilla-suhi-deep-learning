# Methodological workflow

This document summarizes the implemented public workflow for retrospective annual
land surface temperature (LST) modeling in the Barranquilla Metropolitan Area,
Colombia.

The repository documents the computational chain from Landsat 8/9 scene
inventory through annual-product generation, quality control, train-only
normalization, T3 tensor construction, patch extraction, training of models
M1-M5, model comparison, and physical-unit evaluation.

## 1. Workflow scope

The public workflow covers the retrospective component of the study.

It includes:

- Landsat 8/9 scene inventory and annual data organization;
- annual LST and spectral-index generation;
- quality screening and terrestrial-domain definition;
- train-only normalization;
- NASA POWER regional climate-radiative descriptor construction;
- T3 tensor construction;
- spatial patch extraction;
- training of models M1-M5;
- comparative evaluation;
- physical-unit and spatial diagnostics.

The current public repository does not implement:

- CA-ANN/MOLUSCE future land-cover simulation;
- prospective spectral-predictor generation;
- conditioned 2035 LST/SUHI projection.

These prospective components belong to a separate stage of the broader study.

## 2. Landsat 8/9 scene inventory

The workflow begins with Landsat 8 and Landsat 9 Collection 2 Level-2 products
covering the Barranquilla modeling domain.

```text
Operational period: 2013-2025
WRS-2 path/row: 009/052
Projected CRS: EPSG:32618
Nominal spatial resolution: 30 m
```

The year 2026 is excluded because it does not represent a complete annual period
comparable with the preceding years.

The scene inventory records, among other fields:

- scene identifier;
- satellite platform and sensor;
- acquisition date;
- year, month, and day of year;
- WRS path and row;
- cloud-cover metadata;
- processing level;
- collection category;
- spacecraft identifier.

Implementation:

```text
notebooks/01_scene_inventory_landsat_8_9.ipynb
```

Lightweight inventory products are stored in:

```text
data/scene_inventory/
```

## 3. Data sources

The primary satellite collections are:

```text
LANDSAT/LC08/C02/T1_L2
LANDSAT/LC09/C02/T1_L2
```

These products provide:

- atmospherically corrected surface reflectance;
- operational Landsat surface temperature;
- `QA_PIXEL`;
- `QA_RADSAT`.

Daily NASA POWER data are also used to derive annual regional
climate-radiative descriptors for the model-domain centroid. These variables are
not interpreted as spatially distributed climate rasters at Landsat resolution.

## 4. Landsat preprocessing

The preprocessing stage performs:

- filtering by study area, date, and WRS-2 path/row;
- official Collection 2 scale-factor and offset application;
- conversion of LST to degrees Celsius;
- `QA_PIXEL` masking;
- `QA_RADSAT` screening;
- exclusion of unavailable and NoData observations;
- spectral-index calculation;
- annual compositing;
- spatial harmonization;
- valid-observation count generation.

The final spectral predictor set is:

```text
NDVI, NDMI, NDBI, UI, SAVI, BSI
```

The NIR-SWIR1 moisture index is reported as `NDMI`, not `NDWI`, to distinguish it
from the Green-NIR open-water index.

Implementation:

```text
notebooks/02_preprocessing_lst_indices.ipynb
```

## 5. Annual product construction

For each year from 2013 through 2025, valid observations are aggregated using a
pixel-wise annual median.

A pixel is accepted in an annual product only when at least two valid
observations are available for that variable and year.

Annual products are generated for:

```text
LST
NDVI
NDMI
NDBI
UI
SAVI
BSI
```

Valid-observation count layers are also generated as diagnostics.

The resulting annual maps represent comparable annual surface states. They do
not represent daily extremes or intra-seasonal thermal variability.

## 6. Spatial harmonization and modeling domain

All annual products are aligned to a common canonical grid with:

- fixed spatial extent;
- fixed 30 m resolution;
- common `EPSG:32618` reference;
- common affine transform;
- pixel-to-pixel correspondence;
- consistent NoData coding.

Continuous variables are resampled with bilinear interpolation. Discrete
observation-count products are handled with nearest-neighbor resampling.

The final modeling domain is restricted to valid terrestrial surfaces. Open-water
and structurally non-land areas are excluded to avoid mixing land and water
thermal regimes.

Implementation and audit:

```text
notebooks/02_preprocessing_lst_indices.ipynb
notebooks/03_quality_control.ipynb
```

## 7. Quality control

The quality-control workflow checks:

- file existence;
- CRS consistency;
- raster dimensions;
- affine transforms;
- spatial extent;
- valid-pixel counts and fractions;
- physical-value ranges;
- annual observation density;
- missing or inconsistent products;
- terrestrial-domain and common-mask consistency.

Lightweight audit products are stored in:

```text
data/quality_control/
docs/figures/quality_control/
```

## 8. Train-only normalization

Normalization parameters are estimated from training data only and remain fixed
for validation and test years.

### 8.1 LST target

LST is normalized with a global z-score:

```text
LST_z = (LST - mu_train) / sigma_train
```

Parameters are estimated from valid target-year LST observations for:

```text
2016-2019
```

### 8.2 Spectral predictors

Each spectral index is scaled to `[0, 1]` using robust train-only limits:

```text
p2.5 and p97.5
```

Because the training targets are 2016-2019 under the T3 formulation, the
antecedent predictor years used to estimate spectral normalization parameters are:

```text
2013-2018
```

### 8.3 Climate-radiative descriptors

The retained NASA POWER descriptors are normalized using training target years:

```text
2016-2019
```

Implementation:

```text
notebooks/04_train_only_normalization.ipynb
notebooks/05_climate_forcing_integration.ipynb
```

## 9. NASA POWER regional descriptors

The climate workflow initially considers annual regional summaries associated
with:

- 2 m air temperature;
- surface shortwave solar radiation;
- precipitation;
- relative humidity;
- wind speed.

The final M5 configuration uses:

```text
delta_t2m_mean_Y_minus_Yminus1
delta_solar_radiation_mean_Y_minus_Yminus1
```

These variables represent the annual change between target year `Y` and `Y-1`.

They are replicated spatially inside each patch only to ensure tensor
compatibility. This operation does not imply 30 m intra-urban climate variability.

Implementation:

```text
notebooks/05_climate_forcing_integration.ipynb
```

## 10. T3 temporal formulation

The retrospective task uses three antecedent annual states to estimate target-year
LST:

```text
Y-3, Y-2, Y-1 -> LST(Y)
```

The available target years are 2016-2025.

| Split | Target years |
|---|---|
| Training | 2016-2019 |
| Validation | 2020-2022 |
| Test | 2023-2025 |

The observed LST of year `Y` is never used as an input predictor.

M1-M4 use antecedent spectral information only. M5 uses the same antecedent
spectral sequence plus two target-year regional climate-radiative descriptors.

Implementation:

```text
notebooks/06_tensor_construction.ipynb
```

## 11. Tensor organization

For M1 and M2, the three annual states are stacked as 18 channels:

```text
batch x 18 x 128 x 128
```

For M3 and M4, the same information is reshaped as an explicit temporal sequence:

```text
batch x 3 x 6 x 128 x 128
```

For M5, the external patch archive contains:

```text
batch x 20 x 128 x 128
```

corresponding to:

```text
18 spectral channels + 2 regional descriptors
```

Inside M5:

1. the 18 spectral channels are reshaped to `3 x 6`;
2. ConvLSTM produces a 32-channel temporal representation;
3. temporal SE recalibrates the hidden state;
4. the two regional descriptors are concatenated;
5. the resulting 34-channel representation enters the spatial SE U-Net.

## 12. Patch extraction

The implemented patch design is:

```text
Patch size: 128 x 128 pixels
Training stride: 64 pixels
Validation stride: 128 pixels
Test stride: 128 pixels
Minimum valid-pixel fraction: 0.70
```

Each sample contains:

```text
predictor tensor
target LST patch
binary valid-pixel mask
target-year identifier
```

The mask restricts the loss and metrics to valid terrestrial pixels.

The temporal split is assigned before patch extraction. Patches are not randomly
reassigned across training, validation, and test years.

This is a temporally controlled retrospective evaluation, not an independent
spatial-block holdout.

Implementation:

```text
notebooks/07_patch_extraction.ipynb
```

## 13. Model family

The evaluated models are:

| Model | Architecture | Input |
|---|---|---|
| M1 | U-Net baseline | 18 spectral channels |
| M2 | SE U-Net | 18 spectral channels |
| M3 | ConvLSTM U-Net | 3 x 6 spectral sequence |
| M4 | ConvLSTM-SE U-Net | 3 x 6 spectral sequence |
| M5 | T3-Climate ConvLSTM-SE U-Net | 18 spectral channels + 2 regional descriptors |

M1-M4 form the controlled spectral-model comparison.

M5 is an augmented configuration with additional target-year descriptors and a
model-specific training protocol. Therefore, M4-M5 is not interpreted as a pure
ablation of climate augmentation.

Training notebooks:

```text
notebooks/08A_train_M1_unet_baseline.ipynb
notebooks/08B_train_M2_se_unet.ipynb
notebooks/08C_train_M3_convlstm_unet_v5_2016_2025_dirfix.ipynb
notebooks/08D_train_M4_convlstm_se_unet.ipynb
notebooks/08E_train_M5_t3_climate_convlstm_se_unet.ipynb
```

## 14. Training protocol

The common training settings are:

```text
Optimizer: AdamW
Initial learning rate: 1e-3
Weight decay: 1e-5
Loss: masked Huber loss
Huber delta: 1.0
Gradient clipping norm: 1.0
Scheduler: ReduceLROnPlateau
Checkpoint criterion: minimum validation loss
```

Model-specific settings are:

| Models | Batch size | Maximum epochs | Early-stopping patience |
|---|---:|---:|---:|
| M1-M4 | 4 | 100 | 15 |
| M5 | 8 | 120 | 18 |

Validation is used for scheduler control, checkpoint selection, and early
stopping. TEST is reserved for final retrospective evaluation.

## 15. Leakage control

The implemented workflow prevents the main forms of temporal leakage by ensuring
that:

- target-year LST is not used as a predictor;
- antecedent LST is not used as a predictor;
- predictions and residuals are not reused as inputs;
- target years are partitioned before model training;
- normalization parameters are estimated from training data only;
- validation controls checkpoint selection;
- TEST does not control model optimization or checkpoint selection.

The M5 regional descriptors do not contain target-year LST. However, because they
are associated with the target year, M5 is interpreted as a retrospective estimate
conditioned on external covariates, not as an autonomous antecedent-only forecast.

## 16. Model evaluation

The evaluation workflow reports:

```text
RMSE
MAE
Bias
R2
Number of valid pixels
```

Metrics are calculated:

- by split;
- by target year;
- for the aggregated TEST period;
- in normalized units;
- in degrees Celsius after inverse normalization.

The evaluation accumulates sufficient statistics over all valid pixels rather
than averaging independent batch-level metrics.

Implementation:

```text
notebooks/08F_compare_models_M1_M5.ipynb
```

## 17. Physical-unit and spatial diagnostics

Normalized errors are converted to degrees Celsius using the frozen training-only
LST standard deviation.

Notebook 09 generates:

- physical-unit performance tables;
- model rankings;
- TEST year-wise comparisons;
- observed-predicted agreement graphics;
- residual diagnostics;
- manuscript-oriented figures;
- local spatial zoom diagnostics when external full-resolution rasters are
  available.

Implementation:

```text
notebooks/09_physical_unit_evaluation_and_exports_figures.ipynb
```

Patch-based TEST metrics and full-domain mosaic metrics are not assumed to be
numerically identical because they may derive from different spatial aggregation
and overlap procedures.

Local zoom windows are illustrative diagnostics, not independent validation
subsets.

## 18. Repository role and data boundary

The repository provides:

- documentation;
- executable notebooks;
- configuration references;
- environment files;
- lightweight inventory and audit products;
- model comparison and figure-generation logic.

The repository intentionally excludes:

- original Landsat scenes;
- full-resolution annual rasters;
- raster stacks and cubes;
- tensor archives;
- patch datasets;
- model checkpoints;
- full-resolution prediction and residual maps;
- temporary and intermediate products.

Complete regeneration requires the external large-volume products, adequate
storage, and suitable computational resources.

## 19. Final workflow status

The public retrospective workflow is complete for the declared repository scope.

The included notebook sequence covers scene inventory, preprocessing, quality
control, normalization, climate-radiative descriptor construction, tensor
construction, patch extraction, M1-M5 training, model comparison, and physical-unit
evaluation.

No additional public notebooks are planned for this release.
