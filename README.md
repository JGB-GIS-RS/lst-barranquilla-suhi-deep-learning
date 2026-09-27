# Retrospective LST Modeling and Derived SUHI Diagnostics for Barranquilla

Computational workflow for retrospective annual land surface temperature (LST)
modeling and derived surface urban heat island (SUHI) diagnostics in the
Barranquilla Metropolitan Area, Colombia.

The repository integrates Landsat 8/9 Collection 2 Level-2 products,
train-only normalization, regional climate-radiative descriptors from NASA POWER,
and U-Net-based spatiotemporal deep-learning architectures under a temporally
controlled training, validation, and independent TEST design.

---

## 1. Scope

This repository provides the computational record supporting a retrospective
remote-sensing study of annual LST in the Barranquilla Metropolitan Area.

The workflow has two connected components:

1. **Retrospective LST estimation**
   - annual Landsat-derived spectral trajectories;
   - T3 temporal formulation;
   - comparison of five deep-learning configurations;
   - independent temporal evaluation for 2023–2025;
   - full-domain and local spatial diagnostics.

2. **Derived SUHI diagnostics**
   - independent non-urban reference definition using EMC-BUILT 2022;
   - annual reference temperature estimation;
   - observed and predicted continuous SUHI fields;
   - urban SUHI intensity statistics;
   - urban-to-peripheral thermal-gradient analysis.

LST is the only variable directly modeled by the deep-learning framework.
SUHI is subsequently derived from observed and predicted LST using an
independently defined non-urban thermal reference.

Prospective scenario-based projections are outside the scope of this repository.

---

## 2. Study area

The study domain covers the Barranquilla Metropolitan Area and its surrounding
urban, peri-urban, coastal, and fluvial environments in northern Colombia.

```text
Landsat WRS-2 path/row: 009/052
Projected CRS: EPSG:32618 — WGS 84 / UTM zone 18N
Nominal spatial resolution: 30 m
```

---

## 3. Temporal scope

The Landsat analysis covers:

```text
2013–2025
```

The retrospective modeling task uses three antecedent annual spectral states to
estimate the LST of a target year:

```text
Y-3, Y-2, Y-1 -> LST(Y)
```

The fixed target-year partition is:

```text
TRAIN:      2016–2019
VALIDATION: 2020–2022
TEST:       2023–2025
```

The TEST period is kept independent from model training, learning-rate control,
early stopping, hyperparameter adjustment, and checkpoint selection.

---

## 4. Input data

### 4.1 Landsat

The workflow uses Landsat 8 and Landsat 9 Collection 2 Level-2 products:

```text
LANDSAT/LC08/C02/T1_L2
LANDSAT/LC09/C02/T1_L2
```

Annual products are generated from valid observations after quality screening
using `QA_PIXEL`, `QA_RADSAT`, radiometric validity checks, and terrestrial-domain
masking.

The six Landsat-derived spectral predictors are:

```text
NDVI
NDMI
NDBI
UI
SAVI
BSI
```

Annual LST and spectral products are summarized using a pixel-wise median.

### 4.2 NASA POWER

The final climate-augmented model uses two annual regional descriptors:

```text
delta_t2m_mean_Y_minus_Yminus1
delta_solar_radiation_mean_Y_minus_Yminus1
```

These variables represent annual regional conditions associated with the model
domain. They are replicated spatially for tensor compatibility and are not
interpreted as 30 m intra-urban climate fields.

### 4.3 EMC-BUILT

The derived SUHI workflow uses:

```text
EMC-BUILT R2025A
Reference epoch: 2022
```

Dataset DOI:

```text
10.2905/b8e9d5a5-8d2a-427d-be3d-0b4dd533bf4f
```

EMC-BUILT is used as an independent built-up surface product for constructing the
urban support and the non-urban thermal reference.

---

## 5. Spatial harmonization

All annual products are aligned to a common canonical grid:

```text
CRS: EPSG:32618
Resolution: 30 m
Raster size: 1115 × 1257 pixels
```

The processing workflow preserves common extent, affine transform, CRS, and
NoData conventions across model inputs and diagnostic products.

Normalization parameters are estimated from training data only.

LST uses a train-only global z-score, while spectral indices use robust train-only
limits based on the 2.5th and 97.5th percentiles.

---

## 6. T3 formulation

The retrospective model uses three antecedent annual states:

```text
Y-3
Y-2
Y-1
```

to estimate:

```text
LST(Y)
```

The target-year observed LST is used only as the supervised response and is not
included as an input predictor.

Patch configuration:

```text
Patch size: 128 × 128 pixels
Training stride: 64 pixels
Validation stride: 128 pixels
TEST stride: 128 pixels
Minimum valid-pixel fraction: 0.70
```

---

## 7. Deep-learning configurations

Five configurations are evaluated:

| Model | Architecture | Input formulation |
|---|---|---|
| M1 | U-Net | 18-channel spectral T3 stack |
| M2 | SE U-Net | 18-channel spectral T3 stack |
| M3 | ConvLSTM U-Net | Three temporal states × six spectral indices |
| M4 | ConvLSTM-SE U-Net | Three temporal states × six spectral indices |
| M5 | T3-Climate ConvLSTM-SE U-Net | Spectral T3 sequence + two regional climate-radiative descriptors |

M1–M4 share the same spectral predictor content.

M5 is a complete augmented configuration with two additional regional
climate-radiative descriptors and a model-specific training protocol. Therefore,
the M4–M5 comparison is not interpreted as a pure causal ablation of the external
covariates.

The final model configuration is selected using TRAIN/VALIDATION performance.
The 2023–2025 TEST years are reserved for independent evaluation.

---

## 8. Model evaluation

Evaluation metrics include:

```text
RMSE
MAE
Bias
R²
Number of valid pixels
```

Metrics are reported both for the aggregated TEST period and by target year.

For M5, the aggregated independent TEST performance is:

```text
RMSE = 1.813 °C
MAE  = 1.399 °C
Bias = -0.297 °C
R²   = 0.789
```

Patch-based TEST metrics and reconstructed full-domain raster metrics are treated
as distinct evaluation products because they differ in spatial support and
reconstruction procedures.

Full-domain products are used for spatial diagnostics, residual analysis, and
derived SUHI calculations.

---

## 9. Non-urban reference definition

The SUHI analysis uses an independently defined non-urban reference.

EMC-BUILT is harmonized to the 30 m canonical grid using an extensive-area
aggregation approach.

The built-up fraction is defined as:

```text
f_BU = built-up area / 900 m²
```

The general built-up mask is:

```text
BU_all: f_BU >= 0.10
```

The consolidated built-up core is defined using 8-neighbor connected components:

```text
BU_core: connected components >= 0.333 km²
```

The final non-urban reference zone is:

```text
R = 4–6 km from BU_core
    intersect fixed TEST support
    excluding BU_all
```

Final reference geometry:

```text
Pixels: 167,527
Area:   150.7743 km²
```

The 4–6 km band was selected from observed thermal behavior and independent
spectral controls rather than from the resulting SUHI magnitude.

Sensitivity was evaluated for:

```text
A_min = 0.25, 0.333, 0.50 km²
```

---

## 10. Derived SUHI diagnostics

For each TEST year, continuous SUHI is calculated as:

```text
SUHI_obs  = LST_obs  - Tref_obs
SUHI_pred = LST_pred - Tref_pred
```

where `Tref` is the median LST over the valid pixels of the non-urban reference
zone `R`.

The urban support used for summary statistics is:

```text
U = BU_core intersect annual common SUHI support
```

The principal SUHI diagnostics are:

```text
Urban median thermal contrast
Urban P95
Observed and predicted SUHI fields
SUHI residual
Urban SUHI intensity distribution
Urban-to-peripheral thermal gradient
```

The 95th percentile is calculated within the urban support:

```text
P95_urban = P95(SUHI | p in U)
```

SUHI is retained as a continuous variable in degrees Celsius. Qualitative
intensity classes are not used in the principal analysis.

---

## 11. Urban-to-peripheral thermal gradient

The thermal gradient is evaluated across:

```text
Urban core
0–2 km
2–4 km
4–6 km
6–8 km
```

The 8–10 km band is retained only as a diagnostic because its spatial support is
strongly truncated within the study domain.

The 4–6 km band serves as the reference level:

```text
Delta LST_zone = median(LST_zone) - Tref
```

Observed and predicted gradients are compared for each independent TEST year:

```text
2023
2024
2025
```

---

## 12. Repository structure

```text
lst-barranquilla-suhi-deep-learning/
│
├── configs/
│   ├── README.md
│   ├── experiment_metadata.yml
│   ├── model_config.yml
│   ├── paths_example.yml
│   └── training_config.yml
│
├── data/
│   ├── README.md
│   ├── scene_inventory/
│   ├── quality_control/
│   ├── normalization/
│   └── climate_forcings/
│
├── docs/
│   ├── README.md
│   ├── data_sources.md
│   ├── preprocessing.md
│   ├── modeling.md
│   ├── workflow.md
│   ├── reproducibility_notes.md
│   ├── repository_status.md
│   └── figures/
│
├── notebooks/
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
│   ├── 09_physical_unit_evaluation_and_exports_figures.ipynb
│   ├── 10_emc_built_2022_reference_and_suhi_diagnostics.ipynb
│   └── 11_urban_to_peripheral_thermal_gradient_2023_2025.ipynb
│
├── .gitignore
├── CITATION.cff
├── LICENSE
├── README.md
├── environment.yml
└── requirements.txt
```

---

## 13. Notebook sequence

| Notebook | Main purpose |
|---|---|
| `01_scene_inventory_landsat_8_9.ipynb` | Landsat 8/9 scene inventory |
| `02_preprocessing_lst_indices.ipynb` | Annual LST and spectral-index preprocessing |
| `03_quality_control.ipynb` | Raster, mask, and valid-domain quality control |
| `04_train_only_normalization.ipynb` | Train-only normalization |
| `05_climate_forcing_integration.ipynb` | NASA POWER descriptor construction |
| `06_tensor_construction.ipynb` | T3 tensor construction |
| `07_patch_extraction.ipynb` | Patch extraction and temporal partitioning |
| `08A_train_M1_unet_baseline.ipynb` | M1 training |
| `08B_train_M2_se_unet.ipynb` | M2 training |
| `08C_train_M3_convlstm_unet_v5_2016_2025_dirfix.ipynb` | M3 training |
| `08D_train_M4_convlstm_se_unet.ipynb` | M4 training |
| `08E_train_M5_t3_climate_convlstm_se_unet.ipynb` | M5 training |
| `08F_compare_models_M1_M5.ipynb` | M1–M5 comparative evaluation |
| `09_physical_unit_evaluation_and_exports_figures.ipynb` | Physical-unit and spatial LST evaluation |
| `10_emc_built_2022_reference_and_suhi_diagnostics.ipynb` | EMC-BUILT reference definition and derived SUHI diagnostics |
| `11_urban_to_peripheral_thermal_gradient_2023_2025.ipynb` | Urban-to-peripheral observed/predicted thermal-gradient analysis |

---

## 14. Lightweight outputs and traceability

The repository prioritizes compact products that support methodological
inspection and analytical traceability.

Depending on the processing stage, these include:

- scene inventories;
- valid-observation summaries;
- raster-geometry audits;
- train-only normalization parameters;
- NASA POWER annual tables and metadata;
- model-comparison metrics;
- diagnostic plots;
- physical-unit evaluation tables;
- non-urban reference diagnostics;
- thermal-gradient tables;
- SUHI summary tables;
- figure-generation logic.

Large computational products remain external to GitHub.

---

## 15. Data not stored in this repository

The following products are intentionally excluded because of file size,
computational cost, or external-storage requirements:

- original Landsat scenes;
- full-resolution annual GeoTIFF products;
- aligned raster stacks and cubes;
- tensor archives;
- patch datasets;
- trained checkpoints and model weights;
- full-resolution observed/predicted LST rasters;
- full-resolution SUHI GeoTIFF products;
- temporary preprocessing products;
- intermediate Google Drive files.

Consequently, the repository provides substantial methodological and
computational traceability but is not a fully self-contained one-click
reproduction package.

---

## 16. Execution environment

The workflow was developed primarily using:

- Python;
- Google Colab;
- Google Drive;
- Google Earth Engine;
- PyTorch;
- Rasterio;
- NumPy;
- pandas;
- scikit-learn;
- Matplotlib.

Environment references are provided in:

```text
requirements.txt
environment.yml
configs/paths_example.yml
```

Several notebooks reference external project paths that must be adapted when the
original storage structure is unavailable.

GPU resources are recommended for model-training notebooks.

---

## 17. Reproducibility scope

The repository supports:

- inspection of Landsat preprocessing and quality-control logic;
- verification of train-only normalization;
- inspection of T3 tensor construction;
- inspection of M1–M5 architectures and training protocols;
- verification of temporal split and leakage-control logic;
- reproduction of model-comparison calculations when required external products
  are available;
- inspection of full-domain LST evaluation logic;
- reconstruction of the EMC-BUILT-based non-urban reference;
- reconstruction of derived SUHI diagnostics;
- reconstruction of the urban-to-peripheral thermal-gradient analysis.

The notebooks represent the authoritative computational implementations.

---

## 18. Scientific interpretation

The final model should be interpreted as a **retrospective LST estimator
conditioned on antecedent spectral trajectories and target-year regional
climate-radiative descriptors**.

It is not an autonomous antecedent-only forecast model.

The SUHI analysis is a derived diagnostic:

```text
LST
-> independently defined non-urban reference
-> Tref
-> continuous SUHI
-> urban intensity and gradient diagnostics
```

Errors in LST can partially cancel when transformed to an urban–reference
contrast, but differential urban/reference errors and compression of thermal
extremes remain relevant for SUHI interpretation.

---

## 19. Citation

Citation metadata are provided in:

```text
CITATION.cff
```

The bibliographic record of the associated article can be added as the preferred
citation after publication metadata are finalized.

---

## 20. License

License information is provided in:

```text
LICENSE
```
