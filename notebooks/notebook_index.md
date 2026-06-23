# Notebook index

This document defines the planned notebook sequence for the Barranquilla LST/SUHI computational workflow.

The notebooks should be executed sequentially. Each notebook must have a clear objective, defined inputs, defined outputs, configuration references, execution notes, limitations, and reproducibility notes when needed.

## Planned notebooks

| Order | Notebook | Purpose |
|---:|---|---|
| 01 | `01_scene_inventory_landsat_8_9.ipynb` | Build the Landsat 8/9 scene inventory for the 2013-2025 operational period. |
| 02 | `02_preprocessing_lst_indices.ipynb` | Apply masking, extract LST, and compute Landsat-derived annual spectral and thermal products. |
| 03 | `03_quality_control.ipynb` | Evaluate valid pixels, scene statistics, masks, land-domain consistency, and anomalous annual products. |
| 04 | `04_train_only_normalization.ipynb` | Estimate train-only normalization parameters and apply robust scaling to spectral indices and z-score standardization to LST. |
| 05 | `05_climate_forcing_integration.ipynb` | Build and audit annual NASA POWER climate forcing variables, derive anomalies and interannual deltas, and document the final selected climate inputs for M5B. |
| 06 | `06_tensor_construction.ipynb` | Build multi-temporal tensors from aligned raster variables and selected annual climate descriptors. |
| 07 | `07_patch_extraction.ipynb` | Extract spatial-temporal patches for model training and evaluation. |
| 08 | `08_baseline_models.ipynb` | Train and evaluate baseline models. |
| 09 | `09_unet_convlstm_se_training.ipynb` | Train U-Net, ConvLSTM, and SE-based deep learning models. |
| 10 | `10_model_evaluation.ipynb` | Evaluate statistical metrics and compare model performance. |
| 11 | `11_spatial_diagnostics.ipynb` | Generate residual maps and spatial diagnostics. |
| 12 | `12_prediction_maps_hotspots.ipynb` | Generate prediction maps and hotspot diagnostics. |

## Notebook requirements

Each notebook should include:

- objective;
- required inputs;
- generated outputs;
- configuration files used;
- execution notes;
- limitations;
- reproducibility notes when needed.

## Temporal scope

The operational period for the Landsat 8/9 workflow is:

```text
2013-2025
```

The year 2026 is not included in the operational annual analysis because the annual period is incomplete.

## Temporal formulation

The model uses three antecedent annual states to predict the LST of a target year:

```text
T1 = Y - 3
T2 = Y - 2
T3 = Y - 1
Target = LST(Y)
```

## Temporal partitioning

The target-year partitioning strategy is:

- Training target years: 2016-2019
- Validation target years: 2020-2022
- Test target years: 2023-2025

The training period used to estimate normalization parameters is:

```text
2013-2019
```

Validation and test periods must not be used to estimate normalization parameters.

## Normalization strategy

Train-only normalization is implemented as an independent workflow stage in:

```text
04_train_only_normalization.ipynb
```

This notebook estimates:

- global z-score parameters for LST using training data only;
- robust percentile-based scaling parameters for spectral indices using training data only.

The estimated parameters are then fixed and applied consistently to training, validation, and test periods.

## Climate forcing strategy

NASA POWER climate forcing variables are documented in:

```text
05_climate_forcing_integration.ipynb
```

This notebook should:

- build an annual NASA POWER climate table for 2013-2025;
- derive annual descriptors, anomalies, and interannual deltas;
- document evaluated candidate climate variables;
- retain only the final selected interannual climate inputs used by M5B:
  - `delta_t2m_mean_Y_minus_Yminus1`;
  - `delta_solar_radiation_mean_Y_minus_Yminus1`.

NASA POWER variables are annual regional descriptors associated with the model-domain centroid. They should not be interpreted as pixel-level spatial climate rasters.

## Path management

Notebooks should avoid hard-coded personal paths.

Local or cloud paths should be loaded from:

```text
configs/paths_example.yml
```

Users should copy this file as:

```text
configs/paths.yml
```

and modify it locally.

The file `paths.yml` should not be committed to the repository if it contains personal or machine-specific paths.

## Data policy

Large raster datasets, tensors, patches, model checkpoints, and full-resolution outputs should not be uploaded to GitHub.

Lightweight tabular products, JSON summaries, and diagnostic figures may be included when they improve transparency and reproducibility.
