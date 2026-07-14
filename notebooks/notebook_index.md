# Notebook index

This document describes the final public notebook collection included in the
Barranquilla LST/SUHI deep-learning repository.

The numbering reflects the logical order of the computational workflow. The
repository is not a fully self-contained, one-click pipeline because large raster,
tensor, patch, checkpoint, and full-resolution prediction products remain
external to GitHub.

## 1. Public notebook sequence

| Order | Notebook | Main purpose |
|---:|---|---|
| 01 | `01_scene_inventory_landsat_8_9.ipynb` | Build and summarize the Landsat 8/9 Collection 2 Level-2 scene inventory for 2013-2025. |
| 02 | `02_preprocessing_lst_indices.ipynb` | Apply Landsat quality screening, convert variables to physical units, generate annual LST products, and calculate NDVI, NDMI, NDBI, UI, SAVI, and BSI. |
| 03 | `03_quality_control.ipynb` | Audit annual file availability, raster geometry, valid-pixel coverage, common-valid domains, and the final terrestrial modeling mask. |
| 04 | `04_train_only_normalization.ipynb` | Estimate and audit train-only normalization parameters for LST and the six spectral indices, and generate normalized annual products. |
| 05 | `05_climate_forcing_integration.ipynb` | Retrieve and summarize NASA POWER data, derive annual and interannual regional climate-radiative descriptors, and document the two descriptors retained for M5. |
| 06 | `06_tensor_construction.ipynb` | Organize annual spectral and climate-radiative variables into T3 tensors compatible with the retrospective modeling formulation. |
| 07 | `07_patch_extraction.ipynb` | Extract masked spatial-temporal patches and assign them to fixed training, validation, and test target-year partitions. |
| 08A | `08A_train_M1_unet_baseline.ipynb` | Train and evaluate M1, the 18-channel U-Net baseline. |
| 08B | `08B_train_M2_se_unet.ipynb` | Train and evaluate M2, the 18-channel SE U-Net. |
| 08C | `08C_train_M3_convlstm_unet_v5_2016_2025_dirfix.ipynb` | Train and evaluate M3, the ConvLSTM U-Net using an explicit three-state spectral sequence. |
| 08D | `08D_train_M4_convlstm_se_unet.ipynb` | Train and evaluate M4, the ConvLSTM-SE U-Net. |
| 08E | `08E_train_M5_t3_climate_convlstm_se_unet.ipynb` | Train and evaluate M5, the T3-Climate ConvLSTM-SE U-Net with two regional climate-radiative descriptors. |
| 08F | `08F_compare_models_M1_M5.ipynb` | Consolidate split-wise and year-wise metrics and compare M1-M5 in normalized and physical temperature units. |
| 09 | `09_physical_unit_evaluation_and_exports_figures.ipynb` | Convert normalized outputs to degrees Celsius and export model-comparison, agreement, residual, and local spatial-diagnostic figures. |

This collection represents the final public notebook scope of the repository. No
additional public notebooks are planned for this release.

## 2. Workflow organization

The notebooks are grouped into four computational blocks.

### Block A — Landsat products and quality control

```text
01 -> 02 -> 03 -> 04
```

This block documents scene selection, annual LST and spectral-index generation,
quality control, land-domain restriction, and train-only normalization.

### Block B — Climate-radiative integration and sample construction

```text
05 -> 06 -> 07
```

This block documents NASA POWER regional descriptors, T3 tensor construction,
and spatial patch extraction.

### Block C — Model training

```text
08A, 08B, 08C, 08D, 08E
```

These notebooks implement the five model configurations evaluated in the
retrospective experiment.

### Block D — Comparative and physical-unit evaluation

```text
08F -> 09
```

This block consolidates model performance and generates manuscript-oriented
tables and figures.

## 3. Temporal scope and T3 formulation

The operational Landsat period is:

```text
2013-2025
```

The year 2026 is excluded because it does not represent a complete annual period
comparable with the preceding years.

The retrospective task uses three antecedent annual spectral states:

```text
Y-3, Y-2, Y-1 -> LST(Y)
```

The fixed target-year partition is:

```text
Training:   2016-2019
Validation: 2020-2022
Test:       2023-2025
```

The observed LST of target year `Y` is used only as the supervised response. It is
not included as an input predictor.

## 4. Predictor structure

The six Landsat-derived spectral variables are:

```text
NDVI, NDMI, NDBI, UI, SAVI, BSI
```

M1-M4 use only the three-state spectral sequence.

M5 uses the same spectral sequence plus:

```text
delta_t2m_mean_Y_minus_Yminus1
delta_solar_radiation_mean_Y_minus_Yminus1
```

These NASA POWER variables are annual regional climate-radiative descriptors
associated with the model-domain centroid. They are not pixel-level climate
rasters.

## 5. Reference-experiment data note

The model-training notebooks use archived external patch datasets from the
reference experiment reported in the manuscript.

Some external directory names, channel identifiers, and filenames retain
historical development labels, including:

```text
T3_full
T3_full_M5B_delta_t2m_delta_radiation
M5B
```

The public model label is:

```text
M5: T3-Climate ConvLSTM-SE U-Net
```

Notebooks 06 and 07 document the public reconstruction logic for T3 tensors and
patches. The archived datasets loaded by Notebooks 08A-08E correspond to the
reference training experiment and may retain earlier directory or channel naming
conventions.

These historical identifiers do not change the declared predictor variables,
T3 formulation, target-year partitions, model architectures, loss functions, or
evaluation metrics.

## 6. Normalization note

Notebook 04 and the lightweight products under:

```text
data/normalization/
```

document the normalization workflow and the exported parameters associated with
the public processing record.

The training notebooks consume already prepared external patch archives from the
reference experiment. Therefore, the exact normalization values associated with
those archived patches are preserved within the corresponding experiment outputs
and evaluation notebooks.

Validation and test information is not used for model checkpoint selection,
learning-rate control, or early stopping.

## 7. Climate-diagnostic note

Notebook 05 includes annual and full-period climate-error diagnostic products for
methodological traceability.

The two M5 descriptors were retained on the basis of physical consistency and
development-stage diagnostics. Full-period diagnostic tables should be
interpreted as descriptive post hoc audits. They were not used to select
checkpoints, tune hyperparameters, or optimize TEST performance.

## 8. Execution requirements

The notebooks were developed primarily in Google Colab with external products
stored in Google Drive.

Several notebooks therefore contain environment-specific paths that must be
adapted before execution.

Reference files are provided in:

```text
requirements.txt
environment.yml
configs/paths_example.yml
```

The YAML files under `configs/` are structured configuration references. The
notebooks contain the authoritative executed implementations and do not all load
the YAML files automatically at runtime.

## 9. External inputs

Complete execution may require external access to:

- Landsat 8/9 Collection 2 Level-2 products;
- the study-area geometry and canonical raster grid;
- annual LST and spectral-index GeoTIFFs;
- normalization products;
- NASA POWER source tables;
- tensor archives;
- patch datasets;
- model checkpoints;
- full-resolution observed and predicted rasters.

These large products are intentionally excluded from GitHub.

## 10. Public outputs

The repository includes lightweight products that support inspection and
traceability, including:

- scene-inventory tables;
- quality-control reports;
- normalization parameters and audits;
- NASA POWER tables and metadata;
- diagnostic figures;
- stored notebook outputs from the reference execution;
- model-comparison tables and figures.

## 11. Reproducibility scope

The notebook collection supports:

- methodological inspection;
- architecture and training-protocol review;
- verification of temporal partitions and leakage controls;
- partial computational reproduction when the required external inputs are
  available;
- traceability of the retrospective model comparison.

It should not be interpreted as a byte-for-byte reconstruction of every
historical intermediate artifact or as a fully self-contained end-to-end data
package.
