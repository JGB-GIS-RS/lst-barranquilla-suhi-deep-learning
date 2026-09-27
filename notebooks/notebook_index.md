# Notebook index

This document defines the public computational sequence for the retrospective
Barranquilla LST modeling and derived SUHI study.

## 1. Notebook sequence

| Order | Notebook | Role in the workflow |
|---:|---|---|
| 01 | `01_scene_inventory_landsat_8_9.ipynb` | Landsat 8/9 scene inventory. |
| 02 | `02_preprocessing_lst_indices.ipynb` | Annual LST and spectral-index preprocessing. |
| 03 | `03_quality_control.ipynb` | Quality control and terrestrial-domain audit. |
| 04 | `04_train_only_normalization.ipynb` | Train-only scaling parameters and normalized products. |
| 05 | `05_climate_forcing_integration.ipynb` | NASA POWER regional descriptor construction. |
| 06 | `06_tensor_construction.ipynb` | T3 tensor construction. |
| 07 | `07_patch_extraction.ipynb` | Patch extraction and temporal partitioning. |
| 08A | `08A_train_M1_unet_baseline.ipynb` | M1 training. |
| 08B | `08B_train_M2_se_unet.ipynb` | M2 training. |
| 08C | `08C_train_M3_convlstm_unet_v5_2016_2025_dirfix.ipynb` | M3 training. |
| 08D | `08D_train_M4_convlstm_se_unet.ipynb` | M4 training. |
| 08E | `08E_train_M5_t3_climate_convlstm_se_unet.ipynb` | M5 training. |
| 08F | `08F_compare_models_M1_M5.ipynb` | Comparative TEST evaluation of M1–M5. |
| 09 | `09_physical_unit_evaluation_and_exports_figures.ipynb` | Physical-unit LST evaluation and spatial diagnostics. |
| 10 | `10_emc_built_2022_reference_and_suhi_diagnostics.ipynb` | Reference-zone construction and derived SUHI diagnostics. |
| 11 | `11_urban_to_peripheral_thermal_gradient_2023_2025.ipynb` | Final urban-to-peripheral gradient analysis. |


> **Public notebook note:** Notebooks 10 and 11 preserve the approved computational cells of the final executed notebooks. Inline runtime outputs and execution-specific metadata are omitted from the public copies to keep the repository lightweight and avoid environment-specific metadata; the analytical code is unchanged.

## 2. Computational blocks

### Block A — Annual Landsat products and quality control

```text
01 → 02 → 03 → 04
```

### Block B — External descriptors and sample construction

```text
05 → 06 → 07
```

### Block C — Deep-learning model family

```text
08A, 08B, 08C, 08D, 08E
```

### Block D — Independent retrospective evaluation

```text
08F → 09
```

### Block E — Derived SUHI analysis

```text
10 → 11
```

## 3. Temporal formulation

```text
Y-3, Y-2, Y-1 → LST(Y)
```

Target-year partition:

```text
TRAIN:      2016–2019
VALIDATION: 2020–2022
TEST:       2023–2025
```

M1–M4 use the antecedent spectral sequence. M5 uses the same spectral sequence
plus two target-year regional climate-radiative descriptors from NASA POWER.

M5 is interpreted as a retrospective estimator conditioned on external
target-year regional covariates, not as an autonomous antecedent-only forecast.

## 4. Final non-urban reference and SUHI formulation

Notebook 10 uses EMC-BUILT 2022 R2025A to construct:

```text
BU_all  = built-up fraction ≥ 0.10
BU_core = connected components ≥ 0.333 km²
R       = 4–6 km from BU_core, within fixed TEST support, excluding BU_all
```

For each TEST year:

```text
SUHI_obs  = LST_obs  - Tref_obs
SUHI_pred = LST_pred - Tref_pred
```

Urban summary support:

```text
U = BU_core ∩ annual common SUHI support
```

Principal derived diagnostics include urban median SUHI, urban P95, spatial
residuals, sensitivity to BU_core definition, error decomposition, and the
urban-to-peripheral thermal gradient.

## 5. Reproducibility boundary

The notebooks provide executable methodology and stored code paths, but complete
end-to-end regeneration requires external large-volume products that are not
stored in GitHub.

Historical external directory labels such as `M5B` may persist in archived
paths used by training notebooks. The public model designation is M5:
T3-Climate ConvLSTM-SE U-Net.

The public notebook sequence ends at Notebook 11 and corresponds to the final
scope of the retrospective manuscript workflow.
