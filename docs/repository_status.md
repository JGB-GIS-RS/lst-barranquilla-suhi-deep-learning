# Repository status

This document defines the public scope and reproducibility boundary of `lst-barranquilla-suhi-deep-learning`.

## 1. Current scope

The repository is the computational companion for a retrospective study of annual land surface temperature (LST) modeling and derived surface urban heat island (SUHI) diagnostics in the Barranquilla Metropolitan Area, Colombia.

The implemented public workflow covers:

- Landsat 8/9 inventory and preprocessing;
- annual LST and six spectral indices;
- quality control and canonical-grid harmonization;
- fixed pre-validation Landsat normalization;
- NASA POWER regional climate-radiative descriptors;
- T3 tensor and patch construction;
- M1–M5 training;
- independent 2023–2025 TEST evaluation;
- physical-unit and spatial LST diagnostics;
- EMC-BUILT 2022 non-urban reference definition;
- continuous SUHI diagnostics;
- urban-to-peripheral thermal-gradient analysis.

Prospective scenario-based projection is outside the declared repository scope.

## 2. Public notebook sequence

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

## 3. Scientific interpretation

The directly modeled target is LST:

```text
Y-3, Y-2, Y-1 → LST(Y)
```

M5 augments the antecedent spectral sequence with two target-year regional climate-radiative descriptors. It is therefore interpreted as a retrospective conditioned estimator, not an autonomous antecedent-only forecast.

SUHI is derived after LST estimation:

```text
LST → independent non-urban reference → Tref → continuous SUHI
```

## 4. Temporal protocol

```text
TRAIN:      2016–2019
VALIDATION: 2020–2022
TEST:       2023–2025
```

Validation controls checkpoint selection. TEST is reserved for independent final evaluation.

## 5. Normalization status

The repository has been harmonized to the reference-experiment Landsat normalization:

```text
Landsat reference period: 2013–2019
LST: global z-score
mu = 38.48322677612305 °C
sigma = 4.032179355621338 °C
Spectral indices: robust P2–P98 min-max, clipped to [0,1]
```

VALIDATION and TEST do not contribute to estimation of these Landsat parameters. The historical filename `04_train_only_normalization.ipynb` is retained for traceability; `train_only` is a leakage-control label rather than a literal statement that Landsat scaling used only target TRAIN years 2016–2019.

The two NASA POWER descriptors used by M5 are normalized separately from target TRAIN years 2016–2019.

## 6. Final reference definition

```text
BU_all:  built-up fraction >= 0.10
BU_core: 8-neighbor components >= 0.333 km²
R:       4–6 km from BU_core within fixed TEST support, excluding BU_all
```

Final R geometry:

```text
167,527 pixels
150.7743 km²
```

## 7. Included repository components

```text
README.md
LICENSE
CITATION.cff
requirements.txt
environment.yml
configs/
data/
docs/
notebooks/
```

The `data/` directory contains lightweight inventories, audit tables, normalization records, climate summaries, and related compact products.

## 8. Data intentionally excluded

The repository excludes raw Landsat scenes, full-resolution annual GeoTIFFs, aligned raster stacks/cubes, tensor and patch archives, trained checkpoints, full-resolution LST/SUHI rasters, and temporary/intermediate Google Drive products.

## 9. Reproducibility status

The repository supports inspection of preprocessing logic, reference-experiment normalization, temporal partitions and leakage controls, model architectures/training procedures, full-domain LST evaluation logic, EMC-BUILT reference construction, SUHI diagnostics, and the urban-to-peripheral thermal gradient.

It is not a fully self-contained end-to-end data package because large-volume research inputs and outputs remain external.

## 10. Historical identifiers

Archived external paths may retain development labels such as `M5B`. The public manuscript designation is:

```text
M5: T3-Climate ConvLSTM-SE U-Net
```

These historical names are retained only for traceability.

## 11. Final status

The repository is complete for the declared retrospective manuscript scope through Notebook 11. Future changes should be limited to corrections, documentation improvements, publication metadata, or explicitly versioned extensions.
