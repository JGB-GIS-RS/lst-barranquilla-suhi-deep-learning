# Notebook index

This document defines the planned notebook sequence for the computational workflow.

The notebooks should be executed sequentially. Each notebook must have a clear objective, defined inputs, defined outputs, and minimal hard-coded local paths.

## Planned notebooks

| Order | Notebook | Purpose |
|---:|---|---|
| 01 | `01_scene_inventory_landsat_8_9.ipynb` | Build the Landsat 8/9 scene inventory for the 2013–2025 operational period. |
| 02 | `02_preprocessing_lst_indices.ipynb` | Apply masking, extract LST, and compute Landsat-derived spectral indices. |
| 03 | `03_quality_control.ipynb` | Evaluate valid pixels, scene statistics, masks, and anomalous scenes. |
| 04 | `04_tensor_construction.ipynb` | Build multi-temporal tensors from aligned raster variables. |
| 05 | `05_patch_extraction.ipynb` | Extract spatial-temporal patches for model training and evaluation. |
| 06 | `06_baseline_models.ipynb` | Train and evaluate baseline models. |
| 07 | `07_unet_convlstm_se_training.ipynb` | Train the proposed U-Net + ConvLSTM + channel attention model. |
| 08 | `08_model_evaluation.ipynb` | Evaluate statistical metrics and compare model performance. |
| 09 | `09_spatial_diagnostics.ipynb` | Generate residual maps and spatial diagnostics. |
| 10 | `10_prediction_maps_hotspots.ipynb` | Generate prediction maps and hotspot diagnostics. |

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

The operational period for the Landsat 8/9 workflow is 2013–2025.

The year 2026 is not included in the operational analysis because the annual period is incomplete.

## Path management

Notebooks should avoid hard-coded personal paths.

Local or cloud paths should be loaded from:

```text
configs/paths_example.yml
