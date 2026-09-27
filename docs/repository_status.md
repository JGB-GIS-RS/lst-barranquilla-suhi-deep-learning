# Repository status

This document defines the public scope and reproducibility boundary of
`lst-barranquilla-suhi-deep-learning`.

## 1. Current scope

The repository is the computational companion for a retrospective study of annual
land surface temperature (LST) modeling and derived surface urban heat island
(SUHI) diagnostics in the Barranquilla Metropolitan Area, Colombia.

The implemented public workflow covers:

- Landsat 8/9 inventory and preprocessing;
- annual LST and six spectral indices;
- quality control and canonical-grid harmonization;
- train-only normalization;
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

Notebooks 10 and 11 correspond to the final analytical closure of the SUHI
component.


> **Public notebook note:** Notebooks 10 and 11 preserve the approved computational cells of the final executed notebooks. Inline runtime outputs and execution-specific metadata are omitted from the public copies to keep the repository lightweight and avoid environment-specific metadata; the analytical code is unchanged.

## 3. Scientific interpretation

The directly modeled target is LST.

```text
Y-3, Y-2, Y-1 → LST(Y)
```

M5 augments the antecedent spectral sequence with two target-year regional
climate-radiative descriptors. It is therefore interpreted as a retrospective
conditioned estimator, not an autonomous antecedent-only forecast.

SUHI is derived after LST estimation:

```text
LST → independent non-urban reference → Tref → continuous SUHI
```

It is not treated as a second independently trained prediction target.

## 4. Temporal protocol

```text
TRAIN:      2016–2019
VALIDATION: 2020–2022
TEST:       2023–2025
```

Validation controls checkpoint selection. TEST is reserved for independent final
evaluation.

## 5. Final reference definition

The SUHI reference workflow uses EMC-BUILT R2025A, epoch 2022.

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

The operational core threshold is sensitivity-tested at 0.25, 0.333, and
0.50 km².

## 6. Final SUHI diagnostic structure

Urban support:

```text
U = BU_core ∩ annual common SUHI support
```

Primary diagnostics:

- annual reference temperature;
- urban median thermal contrast;
- urban P95;
- observed/predicted SUHI fields;
- SUHI residual;
- urban SUHI intensity distribution;
- urban-to-peripheral gradient;
- sensitivity analysis;
- urban/reference error decomposition.

SUHI remains continuous in degrees Celsius; qualitative intensity classes are not
part of the principal workflow.

## 7. Included repository components

The repository includes:

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

The `data/` directory contains lightweight inventories, audit tables,
normalization records, climate summaries, and related compact products.

## 8. Data intentionally excluded

The following are not stored in GitHub:

- raw Landsat scenes;
- full-resolution annual GeoTIFFs;
- aligned raster stacks and cubes;
- tensor archives;
- patch archives;
- trained checkpoints;
- full-resolution observed/predicted LST rasters;
- full-resolution SUHI rasters;
- temporary processing products;
- intermediate Google Drive artifacts.

This storage boundary is deliberate.

## 9. Reproducibility status

The repository supports:

- inspection of scene-selection and preprocessing logic;
- verification of train-only normalization;
- review of temporal partitions and leakage controls;
- inspection of model architectures and training procedures;
- reconstruction of model-comparison calculations when required external inputs
  are available;
- inspection of full-domain LST evaluation logic;
- reconstruction of the EMC-BUILT reference procedure;
- reconstruction of SUHI summary diagnostics;
- reconstruction of the urban-to-peripheral thermal gradient.

It is not a fully self-contained end-to-end data package because large-volume
research inputs and outputs remain external.

## 10. Historical identifiers

Some archived external patch paths used by the training notebooks may retain
historical development labels such as `M5B`.

The public designation used in the manuscript and repository is:

```text
M5: T3-Climate ConvLSTM-SE U-Net
```

These historical path names are retained only for traceability and do not define
separate scientific experiments.

## 11. Final status

The repository is complete for the declared retrospective manuscript scope
through Notebook 11.

Future repository changes should be limited to corrections, documentation
improvements, publication metadata, or explicitly versioned extensions.
